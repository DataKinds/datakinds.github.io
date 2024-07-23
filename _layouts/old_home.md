---
layout: home_no_about
---

{% capture include_about %}{% include about_include.md %}{% endcapture %}
{{ include_about | markdownify }}

{{ content }}

