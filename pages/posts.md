---
layout: default
title: All Blog Posts
---

# All Blog Posts

Here are all the blog posts I’ve written on web development, programming, and more.

## Latest Posts
{% for post in site.posts %}
### {{ post.date | date: "%B %d, %Y" }} - [{{ post.title }}]({{ post.url }})
{{ post.excerpt }}
{% endfor %}

### Categories

{% for category in site.categories %}
- [{{ category | capitalize }}]({{ site.url }}/categories/{{ category | downcase }})
{% endfor %}
