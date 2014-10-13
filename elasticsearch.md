---
layout: page
title:  mysql
permalink: /elasticsearch/
---

<div class="home">
  <ul class="posts">
    {% for post in site.categories.elasticsearch %}
      <li>
        <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
        <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
      </li>
    {% endfor %}
  </ul>

</div>
