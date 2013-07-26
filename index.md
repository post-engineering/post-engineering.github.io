---
layout: page
title: 
tagline: Homepage
---
{% include JB/setup %}

<ul class="posts">
  {% for post in site.posts %}
    <li>
    	<span>{{ post.date | date_to_string }}</span> &raquo; 
    	<a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a>
    	<p>{{ post.excerpt | markdownify }}</p>
    </li>
  {% endfor %}
</ul>
