---
layout: blog_home
title: welcome to the blog
---
<div class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></div>

i love you for visiting my page. enjoy the plethora of posts made available for your enjoyment and edification below. do note that&nbsp;&nbsp;&nbsp;<img alt="this website is always under construction" height="20px" src="/assets/under-construction.gif">

<div>
    {% if site.posts.size > 0 %}
        {% include post-list.html %}
    {% endif -%}
</div>