---
layout: default
title: Blog Archive
---
<section class="home-panel panel-integration">
<div class="container">
<p class="lead">Blog Posts</p>

{% for post in site.posts %}
    <p>
        <a href="{{ post.url }}">{{ post.date | date_to_string }} - {{ post.title }}</a>
    </p>

  <!--<p>{{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})</p>-->
{% endfor %}

</div>
</section>
