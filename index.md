---
layout: default
title: Home
---

# Welcome to My Personal Website!

[About](/pages/about.md)

This is **Efo**, an undergrad developer.

## Latest Blog Posts

{% for post in site.posts limit: 3 %}
- [{{ post.title }}]({{ post.url }}) - {{ post.date | date: "%B %d, %Y" }}
  {{ post.excerpt }}
{% endfor %}

[See all blog posts](/pages/posts.md)

## Featured Projects

Here are some of the projects I’ve been working on:

[Explore all my projects](/pages/projects.md)
