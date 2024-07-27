---
layout: home
title: browse by tag
permalink: /tags/
---

<div class="tags-expo">
    <div class="tags-expo-list">
    {% for tag in site.tags %}
        <a href="#{{ tag[0] | slugify }}" style="margin-right:10px">{{ tag[0] }}</a>
    {% endfor %}
    </div>
    <div class="tags-expo-section">
        {% for tag in site.tags %}
            {% assign tagname = tag[0] %}
            <h3 id="{{ tagname | slugify }}">{{ tagname }}</h3>
            {% include post-list.html tag=tagname %}
        {% endfor %}
    </div>
</div>
