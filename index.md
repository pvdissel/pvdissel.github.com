---
layout: page
title: Hello World!
tagline: I Code
---
{% include JB/setup %}

## Posts

{% assign posts_collate = site.posts %}
{% include JB/posts_collate %}

{% comment %}
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
{% endcomment %}
