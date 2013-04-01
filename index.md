---
layout: page
title: Hello World!
tagline: begining 
---
{% include JB/setup %}

## My Recent Post 

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## Link of Jekyll

- [Jekyll Quick Start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)
- [Jekyll Bootstrap](http://jekyllbootstrap.com)

