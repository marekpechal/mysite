---
layout: page
title: List of datasheets
tags:
- electronics
---

# Datasheets

{% for fname in site.static_files %}
{% if fname.path contains 'datasheets' %}
<a href="{{ site.baseurl }}{{ fname.path }}">{{ fname.path }}</a>
{% endif %}
{% endfor %}

# Whitepapers

{% for fname in site.static_files %}
{% if fname.path contains 'whitepapers' %}
<a href="{{ site.baseurl }}{{ fname.path }}">{{ fname.path }}</a>
{% endif %}
{% endfor %}

