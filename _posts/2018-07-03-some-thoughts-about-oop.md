---
layout: post
title: Some Thoughts About Pharo Smalltalk and OOP (part 1)
date: 2018-07-03 16:30 -0700
categories: programming smalltalk
---

A while back, I was talking to a friend on the [Esolangs discord server](https://discord.gg/Ua2fxrK). I was in the middle of working on a [project in the Ruby language](https://github.com/aearnus/blackhawk), so we ended up on the topic of Ruby. Me, being a functional programming fanatic, I thought that Ruby's best features were its `#map` and `#inject` and all of the hundreds of functions to work with enumerables and streams. He was on the other end of the spectrum, coming from a fully OOP background, and he appreciated how Ruby handles classes and message sending. 10 minutes of Python bashing later, he mentioned that after having some day-ruining issues with Ruby (*__cough__ the performance __cough__*), he ended up switching over to using Smalltalk in production code.

*Smalltalk*, I thought. I was under the impression that the entire programming world had left Smalltalk, the grand daddy of OOP, back in the 1980's. I was familiar with the concepts that Smalltalk introduced, such as message passing (as mentioned earlier) and classes, but not much more than that. My tiny bit of research that I did before going any further made the language out to be one of those magical "learn this and change your entire perspective" languages, like Haskell or Scheme. Eager to have my mind melded, I decided that I would download a Smalltalk implementation and write this blog post as I was working my way through figuring it out.

[Pharo](https://pharo.org/) has been in the back of my mind since I first saw it on HN a few months ago. It's an entire integrated environment for Smalltalk development, with live coding and hotswapping and an interesting interface and all things that are holy. Their website describes it as "a pure object-oriented programming language and a powerful environment, focused on simplicity and immediate feedback (think IDE and OS rolled into one)". "Pure" and "object-oriented" are two words I've never thought I'd hear in the same sentence, unless there was a "but not" in between them. Haskell's purity comes from the relegation of all external data into the `IO` monad, and the inherent declarativity of representing program flow through function composition. So I'm at a loss for how Smalltalk could achieve the same, or even a similar level of purity. Also, the fact that it's an "IDE and [an] OS rolled into one" is super cool and it makes me hope for something akin to Emacs on steroids.

The First Glance
---
The Pharo launcher starts up and I'm met with an interface to choose what version of the VM (environment? OS? IDE? I'm not sure...) I want to use.

![Pharo launcher](/assets/imgs/some-thoughts-about-oop/0.png)

A version selection menu makes me hopeful for the quality of the software. That's because it makes it obvious that these developers place an importance on backwards compatibility and correctness. Upon selecting the newest development version, it took 10 seconds to download an image and loaded it into the right side of the launcher.

![Loaded version in the Pharo launcher](/assets/imgs/some-thoughts-about-oop/1.png)

I loaded into the environment and the first thing I realized was that it used its own custom widget library. That's some solid dedication, considering that Pharo works on Windows, OSX, and Linux. Here's a picture of it with one of the menus open. It uses an Openbox style interface, where clicking the background opens a little toolbox where you can access the entire interface.

![Pharo interface](/assets/imgs/some-thoughts-about-oop/2.png)

Naturally, I opened up the little "Welcome to Pharo" window and was hit with an interactive text box.

![Welcome to Pharo](/assets/imgs/some-thoughts-about-oop/3.png)

Here's the Emacs-on-steroids I expected. Those little snippets of code to change the theme are clickable and runnable to edit the interface on the fly. I've got a sneaking suspicion that this entire interface is programmed in Pharo itself, and if that's true, I'm super excited to get down to business hacking it.

![!!!](/assets/imgs/some-thoughts-about-oop/4.png)

*Oh hell yeah.*

I'll conclude The First Glance with this screenshot:

![???](/assets/imgs/some-thoughts-about-oop/5.png)

Playing in the Playground
---
In the `Tools` section, there's a window titled `Playground`. I'm assuming that's where you run code. Entering `1 + 1` and pressing the play button gave me back this interface:

![Simple arithmetic](/assets/imgs/some-thoughts-about-oop/6.png)

I clicked around the interface on the right, which describes the return value from that statement up there. The `Raw` tab was pretty boring, just showing what I assume is the raw object representing the number `2`. The `Integer` tab described things about the integer itself, like, for example, that it represents the number `2`. Also wholly uninteresting. The idea that objects can arbitrarily create tabs for exploring them, though, is an interesting idea. Finally, I got to the `Meta` tab, and that's where it got interesting. This tab showed all of the classes (this is probably not the right term to use) that the object inherited.

![The Meta Tab](/assets/imgs/some-thoughts-about-oop/7.png)

This little interface would definitely prove invaluable in an actual working scenario. Alongside listing every ancestor of my little `2` object, it shows all of the methods that are implemented for said object as well.

Some Further Research
---
In part 2 of this blog post, I'll delve a little bit deeper into what it actually means to program in Pharo Smalltalk. Until then, though, I'll leave with a little bit more research that I've done on the Smalltalk language itself. It's built around simplicity and the sole existence of the object. Objects contain a state, pass messages to other objects, and react to messages sent to them. There is nothing else in Smalltalk. For example, methods are implemented by sending a message to an object saying "Please run this method..." (think: `#__send__` from Ruby). For now, thank you for reading this far. If you appreciate what I put out, please feel free to support me on [Liberapay](https://liberapay.com/Aearnus) or [Patreon](https://www.patreon.com/aearnus).
