---
layout: post
title: An Idiot and an Arduino
date: 2018-10-28 17:02 -0700
---
Hello! If you didn't hear, the University of Arizona just hosted the [fourth annual Women's Hackathon](https://womenshackathon.arizona.edu/), a three day event for women in tech and feminist allies. I went to the hackathon and I had a hell of a time. It was my time attending a large tech event like that and I couldn't have chosen a better one to go to. The atmosphere was laid back and extremely inviting, the people there were incredible, and the events and workshops were indispensable. I have nothing but good things to say about the whole event.

Throughout the event, organizers and sponsors were handing out free loot pretty much everywhere you looked. Here's a photo of my pile of stickers post-hackathon:

![Stickers](/assets/imgs/hackathon/IMG_20181028_170938.jpg)

There were quite a few more, but they've all already ended up on my water bottle or on the back of my laptop. I'm not going to get into what my group and I created for the hackathon, but it was an amazing experience to get together with a bunch of people all working towards the same goal in that kind of environment. I'll definitely be writing about that soon.

This all brings me to the point of my post. One of the prizes for participating in the events was an Arduino Uno R3 starter kit.

![The Arduino and friends](/assets/imgs/hackathon/IMG_20181028_172407.jpg)

I've set up a few dumb things with all of this stuff -- playing music by hooking up some wires to a 3.5mm audio jack, or setting up a controller to control an LED, but I'm more or less at a loss for ideas.

One bad idea I've been considering is writing a 6502 emulator for the Arduino. I've written [tons of code in the past to work with the 6502](https://hackage.haskell.org/package/sixty-five-oh-two), and so I know my bearings for the most part. Comparing the specs for the 6502 with the specs of the ATmega328P on the Arduino, this seems like it should be possible.

| Spec | 6502 | ATmega328P |
| --- | --- | --- |
| Max clock speed | 3 MHz | 20 MHz |
| Max addressable RAM | 64 kb* | 8 kb* |
| Registers | 6 | 32 |
| Byte length | 8 bits | 8 bits |

\* the 6502 could _address_ 64 kilobytes of RAM, but most systems that used the 6502 didn't use all 64 kilobytes. The NES only had 8 kb of onboard RAM. This is a bit of an inaccurate measurement anyway, since the ATmega328P is a microcontroller and can't be extended with more RAM -- it's built into the chip itself.

I'd considered actually writing a full pin level emulation layer and allow the entire Arduino board to act as a sort of stand in for a 6502, but there simply aren't enough IO pins on the board to do that. It's possible I could do some weird business with a few shift registers and a circuit that acts as a compatibility layer for the pins on a 6502, but I'm not good enough with electronics to do that and I feel like it'd be incredibly finicky to set up.

In lieu of that, it'd be interesting to write some code that runs the instructions of a 6502 chip on a software level. Considering there wouldn't be much of a way to send 6502 bytecode directly to the Arduino, there's a possibility that I could rig the C++ compiler to simply emit equivalent instructions to a set of 6502 instructions provided at compile time (a la [https://www.youtube.com/watch?v=zBkNBP00wJE](https://www.youtube.com/watch?v=zBkNBP00wJE), which is an _amazing_ talk if you've never seen it before).

All of this is me just brainstorming things to use the Arduino for, since it's not doing much good just sitting there. If you've got any good ideas, please send them my way. I'm a sucker for a good project.
