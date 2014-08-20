---
layout: post
title: "Create Github Blog with Jekyll"
modified:
tags: [jekyll,blog]
---

After spending the whole weekend playing with jekyll, I finally got it working properly. If you follow these steps it will save you a lot more time. 

### Find a Jekyll theme template

[jekyllthemes.org](http://jekyllthemes.org) is the best. Pick a simple one, then clone the git. The theme template already includes markdown files for posts and index pages. Rename the folder to yourgitusername.github.io. 

### Install Jekyll

1. Install Bundler <code>gem install bundler</code>
2. Run <code>bundle install</code> to install all dependencies

I ran into problems with installing Bundler. Here are some tips: 

- Show RubyGems Environment: <code>gem env</code>
- Check ruby version: <code>which ruby</code>

### Configure and run Jekyll

1. Build site <code>jekyll build</code>
2. Run Jekyll <code>jekyll serve</code>
3. Customize the template

After making changes, you must do <code>build</code> <code>serve</code> again to reflect the changes. 

### Deploy to github

1. Create a new repository yourusername.github.io
2. Push your local file to github



