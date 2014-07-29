---
layout: page
title:  mysql
permalink: /mysql/
---

<div class="home">
  <h1>linux</h1>
  <ul class="posts">
    {% for post in site.categories.mysql %}
      <li>
        <span class="post-date">{{ post.date | date: "%b %-d, %Y" }}</span>
        <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
      </li>
    {% endfor %}
  </ul>

</div>
