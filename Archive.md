---
layout: page
title: Archives
---
<ul class="archive">
  {% for post in site.posts %}
    <li>    
        <h4>{{ post.date | date:"%b %d, %Y" }}</h4>
        <div class="archive-post-title"><a href="{{ post.url }}">{{ post.title }}</a></div>
    </li>
  {% endfor %}
</ul>