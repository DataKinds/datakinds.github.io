---
layout: post
title: Modulo Multiplication Circle
date: 2018-04-18 23:43 -0700
categories: programming graphics
---
Without any sort of introduction, here's the video I'm going to be discussing. Even without any explanation, it's beautiful to space out and watch.

<iframe width="560" height="315" src="https://www.youtube.com/embed/iuSwQ_197XU" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

This video depicts modular multiplication on a circle. That is, I've taken a number line (in this case, from 0 to 500) and wrapped it around the outside edge of the circle on the screen. Then, for every point on that circle, I drew a line from its position N to the position N*t mod 500 where t is some function of time. In extremely unmathematical terms, this produced a pretty swirling picture.

Here's the code to reproduce this effect for yourself: [https://gist.github.com/Aearnus/ba8f1b6b5d0e7736e796](https://gist.github.com/Aearnus/ba8f1b6b5d0e7736e796).
