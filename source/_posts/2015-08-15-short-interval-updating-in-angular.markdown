---
layout: post
title: "Short Interval Updating in Angular"
date: 2015-08-15 22:51
comments: true
categories: [Angular]
---

Let's take a look at a problem. We have a webapp to track build times for a continuous integration (CI) server. The main page has an HTML table with dozens of rows (one row for each build) and each row has an entry for the creation time of the build and the length of time it took for the build to be deployed, tested, or whatever else you're doing with your CI server.

For most rows in your table, the duration can be populated with information from the server. The server sends back a creation time, and if the build is finished, a finishing time. Both the creation time and finishing time are specified in a milliseconds version of [Unix time](https://en.wikipedia.org/wiki/Unix_time). The duration is easy enough to calculate. Firstly, in Angular we would opt to use the `ng-repeat` directive to display the data in a table:

<!-- more -->

(I use double square brackets instead of double curly braces in Angular templating here because it conflicts with the rendering engine I use for this blog)

```html
<tr ng-repeat="contentData in content">
    <td ng-repeat="header in headers">
        [[contentData[header].value]]
    </td>
</tr>
```

and a specific `contentData` object would look something like:

```javascript
var contentData = {
    ...
    durationString: getDurationString(msTimeDifferenceInSeconds(
        serverData.creationTime, serverData.finishingTime)),
    ...
};
$scope.content.push(contentData);

function msTimeDifferenceInSeconds(startTime, endTime) {
    var msDifference = endTime - startTime;
    return Math.ceil(msDifference/1000);
}
```

where getDurationString(seconds) is some function that returns a nicer version of a duration in seconds.

```
getDurationString(1) => 1 second
getDurationString(100) => 1 minute, 40 seconds
getDurationString(10000) => 2 hours, 46 minutes, 40 seconds
```

What about the (arguably more interesting) case when a build isn't complete? We want to update the duration every second for every row in the table whose corresponding build is incomplete. We would also want to poll the server every so often so that the table displays the most updated information. How would you do this sort of thing in Angular?

Well the obvious thing to do would be to have a function that updates the specific model data for the build that contains the duration. Store the duration as part of the model data for the build, and update it (and the value that's display in the HTML table through getDurationString) every second. Something similar to the following pseudocode:

```javascript
var durationInterval = $interval(function() {
    for each contentData in $scope.content:
        if !(contentData.isFinished()):
           contentData.duration += 1;
           contentData.durationString = getDurationString(contentData.duration);
}, 1000);

```

and we could have this code run just after loading all the data into the `content` array. We can stop this interval and restart it upon polling for updated data from the server.

Since this is executing once every second, the duration property increments every second. This seems like an okay solution. Angular will automatically update the DOM for us once it sees that the underlying model has changed.

But there's a couple major problems with it. For one, there's a fairly large delay between the first element in the `content` array being populated with the finishing time and the time that the `durationInterval` gets set off (more than 1 second). This effect compounds with other delays and effects the rest of the `content` array as well. We can sort of solve this by calculating the duration again using the current time (`new Date().getTime`) and the known creation time every second, since it's not really that expensive.

A bigger issue still is that it takes time for the DOM to update. Angular needs to realize the `content` array has been changed and update the appropriate parts of the DOM (specifically the duration column in the table for every affected row). This happens in the `$digest` cycle Angular uses to update the DOM upon model changes. This can take a substantial amount of time and adds further delay to the DOM updating.

Implementing this solution, I found the table updates about 10 times in 14 seconds. It should have updated 14 times though. We can't update the model directly and wait for Angular to update the DOM.

An alternative solution would be to update the DOM ourselves. Since this is just a simple table cell and not an input element, Angular won't check it as part of its 2-way data binding procedures. In other words, when we update a table cell, the corresponding model in the Javascript won't change, which is what we want.

We simply want to update the table cell every second. First, we have to find the table cell. One way to do this would be to place a unique id on the table cell

```
<td ng-attr-id="[[header.field + '-' + $parent.$index + '-cell']]" ...>
```
and we can easily find the table cell we wish to modify (the one corresponding to header.field = durationString)

We could parse what's in the table cell (a `durationString` like 3 minutes and 4 seconds) back to a time in seconds (184), add 1, and transform it back to a `durationString`. But that's more work than necessary, and can result in the time slowly drifting (so naturally, I tried that first). We'll just go with the approach of calculating the duration each time for every applicable row (once for each incomplete build), and updating the table cell's `innerHTML` with our updated data.

This solution works super well; the duration field in the table correctly updates once every second. And that's the solution to our problem.

