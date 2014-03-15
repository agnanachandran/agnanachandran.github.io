---
layout: post
title: "How to do X in Octopress"
date: 2013-12-20 23:11
comments: true
categories: [how-to, octopress, tutorial]
---

So I spent the last couple days tweaking my Octopress themes and relearning how to do things since not blogging for a while and getting a new MBP. Navigating the Octopress file hierarchy isn't easy, but hopefully this post will help you (and be a useful reference for myself).

<!-- more -->

So, let's start with a preview of what we have to work with. If you're reading this, I assume you know a bit about Git, the Unix shell, and what Octopress is.

Let's start with the file hierarchy, my current setup, and the Git configuration.

### My current setup:

For starters, I'm using \[oh-my\] zsh with the popular <u>Solarized</u> colour scheme. A typical Octopress folder looks something like this:

``` bash 
$ tree octopress -LF 1 
octopress
├── CHANGELOG.markdown
├── Gemfile
├── Gemfile.lock
├── README.markdown
├── README.md
├── Rakefile
├── _config.yml
├── _deploy/
├── config.rb
├── config.ru
├── plugins/
├── public/
├── sass/
└── source/

5 directories, 9 files
```

A typical Octopress configuration uses two branches (master and source). I use GitHub Pages for deployment and it looks for files in the master branch. But while editing posts and stylesheets, we really only have to worry about the source branch.


### Top-level Configuration
_config.yml holds various top-level/administrative info for your blog. This is where you'd put your blog's name, your Disqus shortname (if applicable), what links you want in your navigation bar, usernames for various services (GitHub, Facebook, G+, etc.), Google analytics tracking ID, and so on.

### Rake Tasks
A quick primer for non-ruby enthusiasts:
`rake` is basically the Ruby version of `GNU make`. Some of the useful rake tasks descriptions are below. Upon executing any of the below rake tasks, a number of useful commands will be run resulting in some otherwise complex thing being completed.

**clean**                     -- clean out caches: .pygments-cache, .gist-cache, .sass-cache

**deploy**                    -- default deploy task

**gen_deploy**                -- generate website and deploy

**generate**                  -- generate jekyll site

**install[theme]**            -- initial setup for octopress: copies the default theme into the path of jekyll's generator.

**list**                      -- list tasks

**new_post[title]**           -- begin a new post in source/_posts

**preview**                   -- preview the site in a web browser

**watch**                     -- watch the site and regenerate when it changes

### Writing a new post

``` bash
$ rake new_post['New post title with Punctuation!']
```

creates a Markdown file in `source/_posts` named "2013-12-28-new-post-title-with-punctuation.markdown", for example.

Deleting the file effectively deletes the post (assuming you haven't already published the post to the interwebs)

If you'd like to preview your post in the browser,

``` bash
$ rake preview
```

will do the trick and show you your blog at localhost:4000 ([more info on localhost](http://whatismyipaddress.com/localhost)). HTTP requests and responses will be logged in the same terminal window, so I recommend opening a seperate tab/window specifically for running this process.

Upon saving the file and refreshing the browser window, you'll see how your post (along with the rest of the blog) will look.

Lastly, saving your post's progress (anything done in the source branch really), can be done through

``` bash
$ git add .
$ git commit -m 'Your helpful message goes here'
$ git push origin source
```

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

A little trick I use when I want to find where a specific css selector's attributes are defined is to use `fgrep`. For example, running

``` bash
$ cd sass
$ fgrep -r 'meta' *
# For long results, pipe to 'less' if desired
```

finds me the exact SASS file(s) I need (and any duplicate declarations that need to be eliminated).

A more detailed description can be found [here](http://octopress.org/docs/theme/).

That's all for now folks; if there's anything you'd like me to add, shoot me an [email](<mailto:anojhgnanachandran@gmail.com>)!
