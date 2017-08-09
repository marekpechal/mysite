---
layout: page
title: Future stuff
tags:
- navbar
---

Here will be links about future stuff.

{% for page in site.pages %}
{% if page.tags contains "future" %}
<h1><a href="{{page.url}}">{{page.title}}</a></h1>
{% endif %}
{% endfor %}


