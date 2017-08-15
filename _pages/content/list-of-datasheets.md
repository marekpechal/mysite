---
layout: page
title: List of datasheets
tags:
- electronics
---

{% for fname in site.static_files %}
{% if fname.path contains 'datasheets' %}
<a href="{{ site.baseurl }}{{ fname.path }}">{{ fname.path }}</a>
{% endif %}
{% endfor %}
