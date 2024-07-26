---
layout: home
title: browse by tag
---

<div class="tags-expo">
    <div class="tags-expo-list">
    {% for tag in site.tags %}
        <a href="#{{ tag[0] | slugify }}" class="post-tag">{{ tag[0] }}</a>
    {% endfor %}
    </div>
    <div class="tags-expo-section">
    {% for tag in site.tags %}
    <h3 id="{{ tag[0] | slugify }}">{{ tag | first }}</h3>
    <ul class="tags-expo-posts">
        {% for post in tag[1] %}
            <li>
                <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
                <h3>
                    <a class="post-link" href="{{ post.url | relative_url }}">
                        {{ post.title | escape }}
                    </a>
                </h3>
            </li>
        {% endfor %}
    </ul>
    {% endfor %}
    </div>
</div>
