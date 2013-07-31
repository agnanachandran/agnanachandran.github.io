---
layout: post
title: ":%s/WordPress/Octopress/g"
date: 2013-07-28 14:00
comments: true
categories: 
---
So after only a few months of using WordPress, I decided to switch to Octopress. I decided to make that obligatory post that every new Octopress user writes. It's some sort of rite of passage, I think.

Anyway, I used WordPress at first because it is by far the most popular blogging platform out there, and was fairly easy to setup with my current hosting provider. I knew about Octopress before I started my WordPress blog but thought it wasn't worth the trouble. I was very wrong. I followed [this tutorial](http://robdodson.me/blog/2012/04/30/custom-domain-with-octopress-and-github-pages/) (with some adjustments due to GitHub Pages now being at ...github.io instead of ...github.com and updated versions of ruby/rvm) and I was up and running the same day.

There's 5 reasons I can think of at the top of my head about why Octopress is so awesome.  

## Super quick deployment; no fumbling with \<insert your favourite blogging platform here\>.

``` bash octopress blogging
rake generate
rake deploy
```

It's that easy. Octopress uses [Jekyll](http://jekyllrb.com/), a static site generator, to push the static HTML, CSS, JS etc. to my master branch at agnanachandran.github.io, and with some quick commands (which I should really make aliases for):

``` bash committing changes
git add .
git commit -m 'new post'
git push origin source
```

my work is pushed to my GitHub repo. 

## Instant previewing

Thanks to the magic of localhost:4000 and the built-in rake tasks, I can easily see what I'm working on in (more or less) real-time.

``` bash rake preview
rake preview
```

So with one command, every time I save, it'll be updated at localhost:4000 (after refreshing, that is). It also just feels really cool; it's much faster and more versatile than, say, editing the HTML yourself with something like Chrome's Inspect Element. That's better used in other places. Leave the on-the-fly editing to `rake preview`.

## Using Vim and Markdown

Probably the biggest reason I chose to start using Octopress over WordPress was that I can use Vim to write my posts. I was tired of dealing with WordPress's built-in editor to edit my posts. It's fine for most people; most people don't know about nor do they need Vim. Highlighting the text with my mouse, what is this, 2013?! Silly GUI.

Additionally, I've been fully immersing myself in as pure of a vim environment as feasible. I've been exclusively using vim key bindings whether it's with actual vim, Eclipse, or XCode (the latter two I use at work). This is just another step of the way to my ultimate goal of writing everything in vim (if only pencil and paper had key bindings too).

Markdown is something I learned about only recently, but it's fairly easy to learn. Turns out that's what I've been using unknowingly on reddit whenever I post a link. It's basically used for any sort of special text; whether it's to make it bold, an h2, or a block of code. 

## This Super Sweet Theme

I found this theme before I switched to Octopress and only days later I made the switch. [Octoflat](http://alexgaribay.com/), has very little to complain about. The fonts and colourscheme are beautiful. The integration with third-party plugins is seamless, and with several one-line-edits, I was able to make that menu bar look the way it does.

## The Experience

A big part of why I try new things is simply for the experience. I learned a ton going through this process and used a bunch of things I've never used before (rake, Jekyll, GitHub Pages, Markdown) while improving my knowledge of things I do know (Vim, subdomains, Git).

Overall, I'm super pleased with Octopress so far and it's been a ton of fun to use. I hope to learn more as I explore its nuances over the next several weeks. 
