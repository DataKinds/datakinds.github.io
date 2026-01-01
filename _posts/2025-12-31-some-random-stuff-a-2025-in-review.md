---
layout: post
title: 'Some Random Stuff: A 2025 In Review'
date: 2025-12-31 14:12 -0700
---

Hi gang. I haven't been writing in a while. And that's okay! I've been reading. And stepping away from the computer. Sometimes even crafting. Buying a house, and building stuff for the house. 

This post is going to be the quickest whirlwind of everything that comes to mind that I'm excited about this year. It won't be in chronological order, and it might not even all be 2025~ I've been spending a lot of

## New jobs!

First half of the year, I was writing Golang for $BIG_FINANCIAL_COMPANY. Second half of the year, I'm writing Golang for $BIGGER_FINANCIAL_COMPANY. Have you _ever_ seen me write Golang? No... sometimes you find the niche, sometimes the niche finds you.

## New servers! New hardware!

I'm building a data storage rack. I've currently got:

* the rack itself
* a power strip
* two Pi 4's
* a shoebox as their containment unit
* a fancy network switch
* and two DVD drives for, you know, ripping old shovelware that's run out its copyright. And other activities of that nature.

Please please please -- if you know of a large 6-10 drive 12in depth 2-3u cost effective drive bay please let me know! It's fine if I get 'em used, I just hardly know where to even look to buy something like this. I used Synology at an old job, but the company's went down the shitter and they lock their new systems down to only use their drives...

## New languages!

I've been working on a LOT of language projects this year. Esolanging has yet again captured my imagination, binding it with a knotty fervor unrivaled by the best sailors. My mind drips sweat. It pants and begs to be let---- wait, where was I? Esolanging, right. I got carried away.

Most of this is imported from Discord. It's much easier for me to pop in there, wax poetic about some fake language for five minutes, and then disappear back into the ether. I've spent a lot of time in the [concatenative discord server](https://discord.gg/9qdcu4X8Be) lately. Join there and join the server in my blog footer, then finish reading.

### Rosin

Hey, this rings a bell! I started [this during last year's december adventure](2024-12-02-2024-december-adventure-log.md). Which also happens to be my last blog post. Oh no, has this blog become a once-a-year venture? I hope not! Writing is invigorating. It just so happens to also be tiring. 

Anyway, Rosin has come incredibly far. [There is a formal spec](https://github.com/DataKinds/tree-rewriter/blob/main/EXECUTION_MODEL.md), there's a [pretty large test suite](https://github.com/DataKinds/tree-rewriter/blob/main/test/Spec.hs), eager variables are actually useful now in that they make the interpreter run until fixpoint on a particular tree. There's a README that is like 6 months out of date (oops! hehe), I've covered most of the features I'm interested in covering.

The compiler's gone through a bunch of iterative improvements that have drastically improved the codebase. Big ups to `@microchipsndip` on Discord and [evincar](https://evincar.com/) for the suggestions. I would have never learned how I'm actually supposed to use `mtl` without these fellas. 

A few functions I'm extra proud of:

* The core pattern matching function: <https://github.com/DataKinds/tree-rewriter/blob/main/src/Core.hs#L237-L276>. I daresay this function almost reads like pseudocode.
* The runtime stepping function: <https://github.com/DataKinds/tree-rewriter/blob/main/src/Runtime.hs#L243-L287>. Finally reaping that sweet, sweet monadic DSL glory.

Next is support for subinterpreters and parallelism (think `Ruby::Box`). Maybe also finally using my Trie implementation to match patterns.

Also, I named this language after an OC. She/they're a demon-woman-changeling with black hair and gray skin that can take the form of a swarm of white-fur-red-eyed mice. Oy vey!

### Objective Thue (Thue++? Thue Plus? Parallel Thue?)

Here's another language idea I've been excited about: a version of Thue where you can pass messages back and forth between different rewriting engines that contain their own internal state. I've been playing around with the syntax and semantics, here's some snippets ripped from discord messages:

```c
invoker {
    default a            // If this scope is invoked without an input, what state do we default to?
    strategy pick-first  // Apply the very first matching rule as close to the start of the string as possible
    b+ :~ $              // This rule (using :~) passes the RHS as a return value of the `invoker`, deleting (this is slow... what do?) the LHS from the internal state.
    a := ab              // This rule (using :=) matches the LHS and substitutes the RHS into the internal state of the `invoker`
}

counter {               // Counts the number of a's it's been passed
    strategy pick-first
    a := I
    . :=                // VERY VERY VERY basic regex, anything I can easily compile to a fast FSM. 
                        // In this case: match any character that's not `a` and delete it from the internal state.
    I+ :::= got $ As    // LHS (I+) is another abuse of basic regex. :::= gives a rule that writes to stdout (similar to Thue proper). Deletes the LHS from the internal state.
                        // $ gives the entire match on the LHS. Think regex substitution syntax: $0, $1, $2, ...
}

invoker2 = new invoker "aaa" // `new` is its own scoped object, just provided automatically by the compiler
counter2 = new counter
counter2 invoker2 

// Prints "got III As", "got IIII As", "got IIIII As", "got IIIIII As", ...d
```

> There are so few knobs to turn here that actually make sense:
> * Where does the rewrite head start
> * What does the state default to
> * How does the rewrite head move when it fails a match
> * How does the rewrite head move when it matches
> * How does the rewriter decide which of many matching rules to select

And a tiny Thue implementation in Raku:

```raku
#!/usr/bin/env raku

grammar Thue {
    token TOP { 
        :my $*PAST-DIVIDER = False;
        \v* [<line> \v*]+ 
    }
    token line {
    || <?{ $*PAST-DIVIDER }> <slurp>
    || <divider>
    || <rule>
    }
    token slurp { (.+) $ }
    token divider { 
        ^^ '---' $$ 
        {$*PAST-DIVIDER = True}
    }
    regex rule { ^^ (.*?) (<becomes>) (\V*) $$ }
    proto token becomes {*}
          token becomes:sym<regular> { ':=' }
          token becomes:sym<output> { ':~' }
}

class RunThue {
    has %.rules;
    method slurp ($/) { 
        my @lhses = %!rules.keys.pick: *;
        my $did = False;
        my $state = $/;
        for @lhses -> $lhs {
            $state.=subst($lhs, { $did = True; %!rules{$_} });
        }
        say $state;
        samewith(self, $state) if $did 
    }
    method rule ($/) { 
        say "got rule!! ", ($0 => $2);
        %!rules{$0} = $2; 
    }
}

Thue.parse: '
1_:=1++
0_:=1

01++:=10
11++:=1++0

_0:=_
_1++:=10

---

_1111111111_', actions => RunThue.new
```

### Some APL with lazy function application, ADTs, and a query-based compiler

As always, my title does the language perfect justice >:)

I've written way more about this in the readme for the language, you ought to just defer to that: <https://github.com/DataKinds/apl>.

### DRILL

It's a subset of <a href="http://sam.cat-v.org/">Sam</a> that I wrote in 2 days for a silly Discord game. The code can be found <a href="https://github.com/DataKinds/drill">here</a>, and I often come back and stare at it because it's the best C99 I wrote this year.

### A Forth, with blackjack, hookers, and row types

You wanted me to write something here? I never promised that. Here's a discord screenshot: ![Big ol discord screenshot that essentially amounts to me reinventing row types from first principals](/assets/imgs/2025-12-31-05-36-00.png)

I'm sorry to those who use screen readers, I am trading speed of writing this article for accessibility here. I gotta run to a NYE party after I post this, and I don't even look cute yet. It's kind of messed up if you really think about it.

Here are the two links from the screenshot. Good stuff!

* <https://dl.acm.org/doi/pdf/10.1145/3290325>
* <https://thunderseethe.dev/series/making-a-language/>

### Game Jams

I signed up for 3, produced 0 games. I need to set my sights lower next time. Bummer!

## Other projects!

### Lil' Weirdo

Crap, everyone's doing AI! **Good thing I did it first.** Predating character.AI, Sora, Meta's fake girlfriends, or that one other discord bot that got banned for endagering kids, I present [Lil Weirdo](https://github.com/DataKinds/lilweirdo).

Lil Weirdo will keep a configurable message log in its special little brain, and it'll use that message log in order to either insult or aggresively try to flirt with other users. Because it also remembers the messages that it previously sent, it usually ends up going all tsundere on you.

It can do a handful of other things too, like generate random video game achievements or random crafting recipies. I think I honed in the prompts pretty well, check them out here: <https://github.com/DataKinds/lilweirdo/blob/main/src/templater.py#L81-L508>.

It's also set up to only use locally run LLMs through Ollama (which I think is adware now, so I gotta find something else).

### Media I've consumed!

Over the last few months I have felt my attention span return to me like a corpse-carrying eagle descending from the sky. It splays out its bounty -- a ton of paperbacks. 

I've been super into horror and sci-fi. Bird Box by Josh Malerman was great. Didn't breathe the whole time. Three Body Problem by Cixin Liu was killer and I'm reading the second one now. I re-read There Is No Antimemetics Department by qntm. 

I'm also gaming. Deadlock has been a godsend this year -- finally, a good MOBA that isn't League of Legends or DOTA 2! I love how they blended the chaos of hero shooters with the chess-ness of MOBAs. This year was another great one for roguelikes: Megabonk, Ball X Pit, and Risk of Rain 2 all had their moments.

# Thank you!

Thank you to all who read this site, thank you to the friends I've made on Discord, and thank the stars that we are afforded another beautiful year. Life would not be as rich without any of this fun stuff to talk about. See ya 2025, happy 2026!
