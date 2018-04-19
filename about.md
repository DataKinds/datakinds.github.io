---
layout: page
title: About me & my projects
permalink: /about/
---
{% capture include_about %}{% include about_include.md %}{% endcapture %}
{{ include_about | markdownify }}
