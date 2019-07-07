---
layout: post
title: '"Perl 6 is Cursed! I hate it!"'
date: 2019-07-06 16:46 -0700
---

_... and other myths people tell themselves to sleep well at night..._

Hatred is a strong emotion, and too often it's one born out of ignorance. I don't like tossing around the phrase "I hate..." often unless it's with regards to something I've researched and well deserves the heavy burden that hatred bears.

To avoid getting too political (that first paragraph was rough at first), I'm going to jump right into my point. I've seen too many people say that they "hate" Perl 6 without taking the time to familiarize themselves with it. I would love to dispel some of the claims that usually go along with this hatred. If you really, truly, honest to God hate Perl 6 you should have concrete reasons why. Elsewise you're just vaguely spitting in the direction of hundreds of people who have produced this thing out of a labor of love. At the very least, spit accurately so we know where we can clean up. Hopefully though this post will convince you to drop that veil of hatred and see the intentions behind these design decisions. The programming community would be a nicer place with less hate.

# The Myths!

Let's get right to all the myths people like to spread. The most common myth I've seen is...

## Myth: Perl 6 will never be finished.

### Reality: Perl 6 has been finished for a long, long time.

Perl 6 is defined as a set of test cases that must be passed by any specific compiler. That test suite is here: [https://github.com/perl6/roast/](https://github.com/perl6/roast/). This test suite has been complete since the language's release, and thus so has the language. Perl 6's compilers may not implement the language in its entirety yet, but that does _not_ mean the language is incomplete.

## Myth: Perl 6 has a bizarre ecosystem.

### Reality: You're probably confusing Perl 5 and Perl 6, or Rakudo itself with Perl 6. Hold on while I explain...

`Perl 6` is sometimes called `Raku` in order to distance it from `Perl 5`. `Perl 6`'s most popular compiler is `Rakudo Star`, which implements `Rakudo Perl 6`. `Perl 6` is built off of a language called `nqp`: Not Quite Perl. `Rakudo Star` uses a virtual machine called [`MoarVM`](https://www.moarvm.org/) which implements the virtual machine that `nqp` is compiled down to. `nqp` is then used to implement the majority of `Rakudo Star`. You read that right: the ubiquitous `Perl 6` compiler is implemented in a stripped down version of `Perl 6` itself. When you type `apt install perl6` (or whatever your equivalent is), your package manager will install `Rakudo Star`. `zef` is the `Perl 6` package manager. `Perl 6` packages live in `p6c` at [`http://modules.perl6.org/`](http://modules.perl6.org/). [`CPAN`](https://www.cpan.org/) DOES host Perl 6 modules, and they are mirrored on the `p6c` website.

## Myth: Perl 6 has no target demographic and no niche.

### Reality: So what?

Larry Wall, Perl's original creator, was a linguist. So let me pose a rhetorical question: what is English's target demographic?

How do you even answer that? Is it tautological? Does it make sense to say that "English speakers" are English's target demographic?

You could argue that this question makes no sense; that Perl 6 is a language constructed by humans for a specific purpose. But English was also constructed by humans, albeit indeliberately. Modern English was constructed from Middle English with various features changed to more closely represent the format of colloquial speech. Perl 6 was constructed out of Perl in a similar way: various features were changed from Perl 5. The specific features that didn't pan out were described in the Perl 6 ["apocalypses"](https://perl6.org/archive/doc/apocalypse.html).

So, Perl 6 came to be as a solution to a problem, and the problem was that Perl 5 wasn't a very good language. Modern English came to be as a solution to the fact that Old and Middle English weren't very good languages -- or, at the very least, they didn't represent their usages any longer. Where does that bring us? I'm not so sure.

Is a language worth it for the sake of its own existence? That's a question for a philosophist, not for me. But if you can't accept that Perl 6 has a valid demographic, then at least consider that much.

To answer the question more directly: Perl 6's lack of a niche _is_ the language's niche. It appeals to functional programmers looking to dip their toes into something imperative, it appeals to low level programmers for having easy C interop, it appeals to array programmers for having extremely flexible containers and operators, etc, etc, etc. Perl 6 allows the programmer to write code in their style that makes sense to them (see: baby Perl 6, as mentioned below) (also see: [https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml)).

## Myth: Perl 6 is inconsistent.

### Reality: [TMTOWTDI](https://en.wikipedia.org/wiki/There%27s_more_than_one_way_to_do_it). There's More Than One Way To Do It.

Language is inconsistent. Ideas are inconsistent. _Reality_ is inconsistent. Perl 6 allows you to choose the best abstraction for the situation. That is the root of inconsistency -- and I don't feel like there's much more to say on that topic.

## Myth: Perl 6 is too complicated and too hard to learn.

### Reality: Perl 6 has layers of necessary complexity.

People who jump right into Perl 6 immediately sink until the complexity piles up over their head. This complexity is necessary. I'm going to quote back to Damien Conway again: 

> One of the original Perl 6 design philosophies was that complexity is intrinsically irreducible... but extrinsically redistributable

([from here](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml))



## Myth: Perl 6 is hard to read.

### Reality: Badly written Perl 6 is hard to read.

### Alternative reality: You cannot read a language which you do not know.

As a native English speaker who only speaks English y una lengua romance, I cannot read Chinese. I cannot even pretend to read Chinese. The symbolic lettering flies right over my puny Western mind. 

If you don't know Perl 6, it will fly right over your head. It is so dense with information that skipping a single symbol might drastically change your interpretation of the entire bit of code. Want to see how dense Perl 6 can be? This is a automatically parallelizable map and fold on a list of numbers 1 to 100, using subroutines `f($x)` to map each number and `g($x, $accum)` to fold the list:

```
[[&g]] (1..100)».&f
```

Every single symbol in that line of code carries a significance, and the code would be drastically different should any of the symbols be missing or different. Imagine trying to read Chinese when you have to skim over every other symbol!

Problem is: there's definitely a limit to how much you should use these information-dense symbols. Admittedly, the precedence rules are confusing and the left & right binding seems to change willy-nilly. If you're from the land of APL you might learn to handle that. If you're from the land of Java you might not. 

Good code and bad code is subjective, but in my subjective opinion: this could lead to bad code, and bad code is hard to read. With great power comes great responsibility.

## Myth: Perl 6 is slow.

### Reality: You're trading off speed for expressiveness, but that speed deficit has been shrinking consistently.


---

Did I miss any myths? Did I leave out an explanation that you like? Do you want to tell me how wrong I am? Please leave a comment using the contact info below! 