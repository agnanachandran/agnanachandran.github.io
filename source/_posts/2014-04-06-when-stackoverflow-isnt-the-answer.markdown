---
layout: post
title: "When StackOverflow isn't the answer"
date: 2014-04-06 20:10
comments: true
categories: 
---

I spent the better half of today debugging one of my current projects, an Android app designed to help users do things on their phone faster. Vague, I know.

The issue at hand was a "java.lang.IllegalStateException" resulting from a fragment not being attached to its activity. Reproducing the bug was easy. Launch the app, search for a YouTube video, back out of the app, and search for another YouTube video. The app crashes. An easy 'solution' was to check `fragment.isAdded()` in the `onVideosRetrieved` callback. Of course, the real problem is that the fragment still wasn't attached to an activity.

> ["A fragment must always be embedded in an activity"](http://developer.android.com/guide/components/fragments.html).

Searching every stackoverflow answer yielded nothing. I tried switching to the non-support version of `fragment`, not using a `FragmentActivity`, and not using the `<fragment>` tag in the activity layout. Nothing changed.

The answer revealed itself to me after reading a tangentially related answer

> ["This can happen if you're trying to keep references to your Fragments, rather than accessing them via the FragmentManager."](http://stackoverflow.com/questions/11536166/android-get-activity-returns-null)

I immediately remembered all the "Manager" classes I had, which for some reason, I decided should be singletons.

    public static WeatherManager getInstance(Fragment fragment, OnFinishedListener listener) {
        if (instance == null) {
            instance = new WeatherManager(fragment, listener);
        }
        return instance;
    }

Oops.

The static reference to the WeatherManager instance is of course kept across the lifetime of the app, and thus, so is the fragment, and its reference to its activity. Big mistake. Fragments are detached from their activity as necessary when the app is backgrounded. By maintaining this static reference, not only was more memory being consumed, but the fragment couldn't reattach itself to the activity (presumably due to the logic present in `Fragment.java`).

What did I learn from all this?

1) Don't use singletons if you don't need to. Or rather, make sure you understand the reason you're using them in the first place.
2) StackOverflow is a tremendous help in most situations. And it was a great help here too (so my title is a lie). [Sometimes, you have to figure it out yourself](https://xkcd.com/979/). And if/when you do, you'll probably facepalm.

Singletons. Not even once.
