---
layout: blog_home
title: welcome to the blog
---
<div class="rss-subscribe">subscribe <a href="{{ "/feed.xml" | relative_url }}">via RSS</a></div>

i love you for visiting my page. enjoy the plethora of posts made available for your enjoyment and edification below. do note that&nbsp;&nbsp;&nbsp;<img alt="this website is always under construction" height="20px" src="/assets/under-construction.gif">

want to link to this page from your blog? you can embed one of my shiny new 88x31s!
<div class="flex" style="justify-content: space-around; flex-wrap: wrap">
    <div style="text-align: center">
        <a href="https://datakinds.github.io/blog"><img src="/assets/88x31_light.png"></a>
        <pre style="max-width: 40vw" class="highlight language-">&lt;a href="https://datakinds.github.io/blog">&lt;img src="https://datakinds.github.io/assets/88x31_light.png"&lt;/a></pre>
    </div>
    <div style="text-align: center">
        <img src="/assets/88x31_dark.png">
        <pre style="max-width: 40vw" class="highlight language-">&lt;a href="https://datakinds.github.io/blog">&lt;img src="/assets/88x31_light.png"&lt;/a></pre>
    </div>
</div>

<div>
    {% if site.posts.size > 0 %}
        {% include post-list.html %}
    {% endif -%}
</div>