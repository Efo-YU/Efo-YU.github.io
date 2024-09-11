---
layout: default
title: Posts Index
---

# All Posts

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url }})
{% endfor %}
