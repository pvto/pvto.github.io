---
layout: page
title: Bleeding poetry
tagline: Experiments, code
---
{% include JB/setup %}

## Recent posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

###Thanks for reading!
(You can contact me if you did not understand something or would like to toss up some points.)

