---
layout: post
title: "Note for build blog with jekyll"
description: "memo for jekyll commands"
category: site
tags: [jekyll]
---
{% include JB/setup %}

Content of this blog is base on [Jekyll Quick Start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)

## Run Locally ##

- First all, need to install tool `jekyll`

    $ get install jekyll

- After pull jekyll code and configured, then can start a local server. Open http://localhost:4000/ to access it.

    $ cd USERNAME.github.com
    $ jekyll --server


## Create content ##

- Create a post. New file is created under folder `./_post`

    $ rake post title="Hello World"

- Create a page

    $ rake page name="about.md"
    $ rake page name="pages/about.md"
    $ rake page name="pages/about"    # will create file ./pages/about/index.html

## Publish ##

- Commit blog change to github

    $ git add .
    $ git commit -m "a new blog"
    $ git push origin master



