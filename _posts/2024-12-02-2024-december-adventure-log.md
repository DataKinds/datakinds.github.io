---
layout: post
title: 2024 December Adventure Log
date: 2024-12-02 09:28 -0700
tags: software december-adventure
---


Hello all! I will be participating in the 2024 [December Adventure](https://eli.li/december-adventure), and this is my captain's log for the month. From Eli's page:

> The [Advent of Code](https://adventofcode.com/) is cool, but a lot, and not everyone’s jam.
> The December Adventure is low key. The goal is to write a little bit of code every day in December.
> ## How?
> Pick a project, or projects and work on them a little bit every day in December.

# Day 2 (12/2/24)

Got this page set up and did a little brainstorming for what I want to work on this month. I know that there are two things off the top of my head I wanted to hammer out:

* I wanted to get an 88x31 badge set up for my website, along with a little area to display others' 88x31s. [yui](https://zptr.cc/) asked me last month and I still have not bothered...
* I want to work more on [Rosin](https://github.com/DataKinds/tree-rewriter) -- specifically, fleshing out the pattern variables to allow any variable to specify its eagerness, and adding syntax for rewriting a global state on top of the s-expr rewrite pointer. Something like

```
(; $comment) ~> 

(; (This rewrite has no state attached))
hello ~> world

(; (This rewrite only runs if there's a `ready` in the global bag))
hello |ready|~|done| world

(; (So we need new syntax for "lambda rules", rules which only run once. We use a backslash in place of the tilde.))
(; (Here's a "lambda rule" which adds a `ready` to the bag. The following rules are equivalent.))
\|ready|
||\|ready|
```

DAY 2 UPDATE 1: I ended up finishing a pretty good majority of the refactor that was required to flesh out pattern variables in Rosin! Check out the big ol' git diff [here](https://github.com/DataKinds/tree-rewriter/pull/1/files).

# Day 3 (12/3/2024)

I may or may not have stayed up past my bedtime working on Rosin... I'm passing all my regression tests on the new pattern variable branch!! Now we can swap eagerness on and off on all variables, even special accumulators. Tomorrow (today, after a sleep) I should write some more regression tests, for the pack/unpack accumulators and for the universal eagerness switch. 

On the left, some of my regression tests. On the right, my passing test dashboard!

![A snippet of my regression suite](/assets/imgs/december-adventure/2-regressionsuite.png){: width="400" } ![My passing regression tests!](/assets/imgs/december-adventure/2-regressionpass.png){: width="700" }

UPDATE 1: it's 10:18pm, I just finished up a minor redesign of my site. Hopefully this color scheme is easier to read, and the header is no longer the size of the screen + it's no longer begging for your attention. The mobile version of the site was also improved, so it should no longer be impossibly narrow on narrow screens. Let me know how it works!!

Update 2: Merged in my pattern variable refactor in Rosin and that simplified things nicely. I also [split apart the regression tests to make them stop sharing state](https://github.com/DataKinds/tree-rewriter/tree/main/sample/regression) and updated the readme to reflect the new syntax.

# Day 4 

You know, I really don't have to put the dates on the headings of each of these entries.

Today I worked a bit more on souping up the tests in Rosin. I made tests be [printed to standard out](https://github.com/DataKinds/tree-rewriter/commit/7a8da5a748d455e11f78e04ef16417323614cfdf#diff-0a88c502e6b2fded6c22871f555f64a0da4a9fc0213e4b7b067f15f51f2780be) instead of requiring the compiler to output the final state. I also separated all the [test files into multiple different files](https://github.com/DataKinds/tree-rewriter/tree/main/sample/regression) so they run in their own contexts.

All of this makes running tests quite nice. The Makefile lets me run specific sets of tests too.

![Makefile running just one set of tests](/assets/imgs/december-adventure/4-tests1.png)
![Makefile running all my tests](/assets/imgs/december-adventure/4-tests2.png)

I also fixed some (but not all) bugs with the accumulators I uncovered while writing extra tests. For example, the Pack and Unpack accumulators don't work quite how I want them to. They don't quite walk the s-expression fully and it's quite buggy.

```
((pack ?@) ~> ?@)
(pack (this (is a (strangely) nested) list)) 
 --- became --- 
(this is a) 
```

I'll have to deal with that soon. For something I fixed... turns out if you matched multiple values against the negate accumulator, they'd all come back backwards.

```
((negate ?- ?- ?-) ~> ?-)
(negate 1 2 3) 
 --- became ---
(-3 -2 -1)
```

That's fixed now. Progress!

# Day 5

Didn't do a lot of coding today. Between moving, work, and having a house guest over tonight I simply wasn't sitting at the computer for very long. 

I got about an hour to myself. I started thinking a bit about setting up a runtime state abstraction in Rosin. This will let me add a multiset bag, support one time lambda rules, and set up a better more explicit ordering for how rules are ingested and then applied.

My changes are still very preliminary, [I posted them up onto a separate branch](https://github.com/DataKinds/tree-rewriter/pull/2/files). 

For background entertainment, I was watching this today: 

<iframe width="560" height="315" src="https://www.youtube.com/embed/GlPREQ55kzc?si=_2kTb6gSJyVrsogX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe> 

# Day 9

Whew. I am sorry for the lack of updates, but the last couple of days flew by. On the 6th and the 7th, my partner and I were going back and forth to the house we're moving into. On the 7th and 8th our power was out. To be more specific: the power was out in exactly half of our unit -- and my computer's outlet was unfortunately affected. All the while I have work and other shenanigans that get in the way. Le sigh... we cringe on...

<iframe width="560" height="315" src="https://www.youtube.com/embed/NmACjr4mAIY?si=DdYx7fkfBZh3H7mf" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

I worked on Rosin a little bit tonight. I fixed a bug in the new [tree zipper](https://wiki.haskell.org/Zipper) datatype [where moving the zipper down to the first child](https://github.com/DataKinds/tree-rewriter/blob/main/src/Zipper.hs#L81-L90) would [reverse the right hand side of the context](https://github.com/DataKinds/tree-rewriter/commit/9aebbc03a45af3b47bc615feac96be6e9368a0ca#diff-37b5a1b9dce90ec661a8a9b74adc0b64c58e8c2c62b9717cbcacefff2b45074eL51). I suppose it made sense at the time but hindsight is 50-50 and I cursed my past self. So the zipper works correctly now which is pretty cool. It worked well enough [so I merged the zipper into main :3](https://github.com/DataKinds/tree-rewriter/commit/5285ea99ed5b5acc8dbd584e12eef09563c3d0f0)

I have been listening to a lot of Remi Wolf.

<iframe style="border-radius:12px" src="https://open.spotify.com/embed/track/1Wi1XpdZzGVIdRTzlTrIEF?utm_source=generator" width="100%" height="152" frameBorder="0" allowfullscreen="" allow="autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture" loading="lazy"></iframe>

# Day 10

Rosin progresses little by little. You can now define single use rules with `~`, like so:

```
$ rosin -p
(/evil/ ~ "$<good$>") "the evil wizard" "the evil tower"


+-----------------+
| Final transform |
+-----------------+
"the good wizard"
"the evil tower"
```

So that will definitely allow for some fun stuff. 

I want to start writing more interesting programs in Rosin, but I keep going down development rabbitholes. The final rabbithole is nearing its conclusion: I have hashed out how I want multiset state rules to look. You will be able to interact with the multiset bag on the rewrite head with the `(:needs | :gives)` pattern. The same function that matched `~>` rules WILL SOON know how to match all variations of these `|` rules... [but it's not quite there yet](https://github.com/DataKinds/tree-rewriter/blob/main/src/Runtime.hs#L105).

```        
$ rosin-can't-do-this-quite-yet -p

(|(apple pear orange peach)) puts an apple, pear, orange, and peach into the bag
(apple |) takes an apple out of the bag
(apple |) doesn't match a second time 

+-----------------+
| Final transform |
+-----------------+
puts an apple, pear, orange, and peach into the bag
takes an apple out of the bag
(apple |) doesn't match a second time
```

Both sides do not need to be specified. `|` indicates a multiset matching rule that will only run once, and `|>` rules will run ad infinitum.

```
( (pear apple grape peach) |> ((fruit salad)) )
now the combination of pear, apple, grape, and peach will always become (fruit salad)
```

Wait, I heard you say: `(fruit salad)`? Like, you put an S-expression into the multiset? Heck yeah I did! [The full range of Rosin values can go into the multiset](https://github.com/DataKinds/tree-rewriter/blob/main/src/Runtime.hs#L45) and this means we'll be able to hijack the pattern variable machinery to do some very cute things:

```
( (sword (stone :name)) | ((sword :name)) )

( | (stone "King's stone"))
( | sword)
will produce one thing in the bag: (sword "King's stone")
a sword's strength is derived from its legendary name
```

Last but not least -- you'll be able to devise a rule that only matches the S-expression tree when something is in the multiset bag. Or a tree rewrite rule that adds something into the bag. The `&` symbol will be able to combine a multiset rewriting rule with a tree rewriting rule, such that the combined rule only matches when both conditions are satisfied. This has some crazy potential imo, and I just nailed down the syntax tonight:

```
(| n & hello ~> world) 
produces an n to the bag every time it matches `hello`

(n n | m & (; :comment) ~>) 
limits the amount of comments you can write to half the `hello`s you write... better be polite!
```

Some of this is implemented, some of it isn't... but I feel good having this all theorycrafted and the way forward from here seems clear to me.