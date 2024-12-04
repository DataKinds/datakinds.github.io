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