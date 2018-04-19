---
layout: post
title: The Ultimate Font
date: 2018-04-18 23:16 -0700
categories: programming graphics
---
So I happened upon [http://resl.xyz/average-font/](http://resl.xyz/average-font/) and I wanted to create something similar. Previously, I had made a dumb, possibly ToS breaking script that scrapes [http://dafont.com/](http://dafont.com/) and downloads the top 1000 or so fonts. You can download that script [here](https://gist.github.com/Aearnus/8365053c42cedb812a2fbb998535d9a7). Some of these fonts are incredibly absurd, ranging from fonts you can use to make your own cool-looking arrows, all the way to peculiar doodles of fruit. With all these out-there fonts, the resulting average font was bound to look nowhere near as good as the one from the aforementioned link, that presumably used nice curated typefaces from similar families.

I threw together a quick script to generate a picture for every letter in the alphabet, from every font on your computer. For me, at least, that’s 1837 fonts, times 26 letters, coming out to a whopping 47,762 letters and pictures of fruit. Alongside this, I also made a script to average all these fonts together and apply a nice threshold over all of them (65% was a good value, from my testing). Those scripts can be found [here](https://gist.github.com/Aearnus/76c73a302c14e11fd8c8e435b94e63da). They require Ruby 2.something and a recent version of ImageMagick.

Without further ado, here is the _ultimate_ font:
{% assign alphabet = "abcdefghijklmnopqrstuvwxyz" | split: "" %}
{% for letter in alphabet %}<a style="display:inline;" href="{{ "/assets/imgs/the-ultimate-font/" | append: letter | append: ".png" | absolute_url }}"><img style="display:inline;" width="100" height="100" src="{{ "/assets/imgs/the-ultimate-font/" | append: letter | append: ".png" | absolute_url }}" alt="The letter {{letter}}"/></a>{% endfor %}
