---
layout: post
title: "Note for build blog with jekyll"
description: "memo for jekyll commands"
category: site
tags: [jekyll]
---
{% include JB/setup %}

Content of this blog is base on [Jekyll Quick Start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)

Run Locally
-----------

- First all, need to install tool `jekyll`

{% highlight console %}
    $ get install jekyll
{% endhighlight %}

- After pull jekyll code and configured, then can start a local server. Open http://localhost:4000/ to access it.

{% highlight console %}
    $ cd USERNAME.github.com
    $ jekyll --server
{% endhighlight %}


Create content
---------------
- Create a post. New file is created under folder `./_post`

{% highlight console %}
    $ rake post title="Hello World"
{% endhighlight %}

- Create a page

{% highlight console %}
    $ rake page name="about.md"
    $ rake page name="pages/about.md"
    $ rake page name="pages/about"    # will create file ./pages/about/index.html
{% endhighlight %}

Publish
-------

- Commit blog change to github

{% highlight console %}
    $ git add .
    $ git commit -m "a new blog"
    $ git push origin master
{% endhighlight %}



