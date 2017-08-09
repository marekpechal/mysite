---
layout: page
title: Under construction
tags:
- navbar
---

These pages are currently empty:

{% for page in site.pages %}
{% if page.tags contains "empty" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

These are under construction:

{% for page in site.pages %}
{% if page.tags contains "under-construction" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}

