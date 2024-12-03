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

DAY 2 UPDATE: I ended up finishing a pretty good majority of the refactor that was required to flesh out pattern variables in Rosin! Check out the big ol' git diff [here](https://github.com/DataKinds/tree-rewriter/pull/1/files).