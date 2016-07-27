---
layout: default
title: Blog Archive
---
<p class="lead">Blog Posts</p>

{% for post in site.posts %}
  {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}
