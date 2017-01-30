---
layout: default
title: Homepage
---

# Hi! 👋


## You can reach me at {{ site.data.social.email }}.

## My posts
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
      {{ post.excerpt }}
    </li>
  {% endfor %}
</ul>
