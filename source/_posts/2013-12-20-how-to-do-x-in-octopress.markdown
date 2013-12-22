---
layout: post
title: "How to do X in Octopress"
date: 2013-12-20 23:11
comments: true
categories: [how-to, octopress, tutorial]
---

This post is perpetually a work in progress.

*Last updated Dec 21, 2013.*

I spent the last couple days tweaking my Octopress themes and re-figuring out how to do things since getting one of the new Haswell MBPs (and thus switching my primary OS from Ubuntu to OS X). Navigating the Octopress file hierarchy isn't easy, but hopefully this post will help you (and be a useful reference for myself).

<!-- more -->

So, let's start with a preview of what we have to work with. If you're reading this, I assume you know a bit about Git, the Unix shell, and what Octopress is.

Let's start with the file hierarchy, my current setup, and the Git configuration.

### My current setup:

1. Insert image 1

You'll notice right away that we're in the source branch. A typical octopress configuration uses two branches (master and source). I use GitHub Pages for deployment and it looks for stuff in the master branch, but we really only have to worry about the source branch. Most of the folders and files are under the source branch with the exception of `/_deploy`, whose contents are generated with `rake deploy` (I'll talk about the various rake tasks later).

For starters, I'm using \[oh-my\] zsh with the popular <u>Solarized</u> colorscheme.

Taking a look at a typical octopress setup, we see some familiar READMEs, some typical files found in most Ruby projects (Gemfile, Rakefile, etc.) that we don't need to worry about for now, and _config.yml.

### Top-level Configuration
_config.yml holds various top-level/administrate info for your blog. This is where you'd put your blog's name, your disqus shortname (if applicable), what links you want in your navigation bar, usernames for various services (GitHub, Facebook, G+, etc.), Google analytics tracking ID, and various other options.


### Rake Tasks
A quick primer for non-ruby enthusiasts:
`rake` is basically the Ruby version of `make`. Upon executing any of the below rake tasks, a number of useful commands will be run resulting in some otherwise complex thing being completed.


2. Insert image 2

The most important of these rake tasks, namely `generate`, `deploy`, `new_post`, `watch`, will be discussed below

### Writing a new post

``` bash
rake new_post['New foobar with Punctuation!']
```

creates a Markdown file in source/_posts called something like "2013-12-21-new-foobar-with-punctuation-exclamation"

Deleting the file effectively deletes the post (assuming you haven't already published the post to the interwebs)

If you'd like to preview your post in the browser,

``` bash
rake preview
```

will do the trick and show you your blog at localhost:4000 ([more info on localhost](http://whatismyipaddress.com/localhost)). HTTP requests and responses will be logged in the same terminal window, so I recommend opening a seperate tab/window specifically for running this process.

Upon saving the file and refreshing the browser window, you'll see how your post (along with the rest of the blog) will look.

### Changing themes

``` bash
$ cd octopress
$ git clone GIT_URL .themes/THEME_NAME
$ rake install['THEME_NAME']
$ rake generate
```

A number of themes are available [here](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes). I've opted for the ["villainy"](https://github.com/mikeclarke/villainy-octopress-theme) theme with several personal customizations. I'll detail how I did those changes below.

### Make your blog feel like home

Disclaimer: If you're anything like me, you may spend several hours tweaking the various SCSS, HTML, and JS files at your disposal. It's pretty addicting.

A more detailed description can be found [here](http://octopress.org/docs/theme/).
To be continued...
