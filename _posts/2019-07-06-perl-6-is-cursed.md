---
layout: post
title: '"Raku is Cursed! I hate it!"'
date: 2019-07-06 16:46 -0700
tags: raku
---

_... and other myths people tell themselves to sleep well at night..._

Hatred is a strong emotion, and too often it's one born out of ignorance. I don't like tossing around the phrase "I hate..." often unless it's with regards to something I've researched and well deserves the heavy burden that hatred bears.

To avoid getting too political (that first paragraph was rough at first), I'm going to jump right into my point. I've seen too many people say that they "hate" Raku without taking the time to familiarize themselves with it. I would love to dispel some of the claims that usually go along with this hatred. If you really, truly, honest to God hate Raku you should have concrete reasons why. Elsewise you're just vaguely spitting in the direction of hundreds of people who have produced this thing out of a labor of love. At the very least, spit accurately so we know where we can clean up. Hopefully though this post will convince you to drop that veil of hatred and see the intentions behind these design decisions. The programming community would be a nicer place with less hate.

# The Myths!

Let's get right to all the myths people like to spread. The most common myth I've seen is...

## Myth: Raku will never be finished.

### Reality: Raku has been finished for a long, long time.

Raku is defined as a set of test cases that must be passed by any specific compiler. That test suite is here: [https://github.com/perl6/roast/](https://github.com/perl6/roast/). This test suite has been complete since the language's release, and thus so has the language. Some Raku compilers may not implement the language in its absolute entirety yet, but that does _not_ mean the language is incomplete.

Rakudo, the most popular Raku compiler, [implements a vast majority of this specification](https://github.com/rakudo/rakudo/blob/master/t/spectest.data.6.c). This is a majority large enough that you will most likely never run into the edge cases which are left unimplemented. I've personally only run into an edge case like this once, and it was during some intense hacking on [an object's metaobject](https://docs.perl6.org/type/Metamodel::ClassHOW). Definitely not something you'd see on a day to day basis.<sup>[2]</sup>


## Myth: Raku has a bizarre ecosystem.

### Reality: You're probably confusing Perl 5 and Raku, or Rakudo itself with Raku. Hold on while I explain...

* `Raku` is the language. It is sometimes called `Raku` in order to distance it from `Perl 5`. 

* `Rakudo` is the most popular `Raku` compiler, and it implements a superset of the language called `Rakudo Raku`. 

* `Rakudo Star` refers to the packaging of the `Rakudo` compiler alongside a smattering of high quality community packages.

* `nqp` is a subset of `Raku` which is used to build the language. You read that right -- `Raku` is implemented using a stripped down version of itself. A large portion of `Rakudo` is written in `nqp`.

* [`MoarVM`](https://www.moarvm.org/) is the virtual machine used by `Rakudo` which implements the backend that `nqp` is compiled against. 

* `zef` is the `Raku` package manager, which downloads a list of modules from a master repo.<sup>[3]</sup>

## Myth: Raku has no target demographic and no niche.

### Reality: Raku is targeted towards problem solvers.<sup>[4]</sup>

(You should read this comment for an interesting analysis: [https://news.ycombinator.com/item?id=20375080](https://news.ycombinator.com/item?id=20375080))

Larry Wall, Perl's original creator, was a linguist. So let me pose a rhetorical question: what is English's target demographic?

How do you even answer that? Is it tautological? Does it make sense to say that "English speakers" are English's target demographic?

You could argue that this question makes no sense; that Raku is a language constructed by humans for a specific purpose. But English was also constructed by humans, albeit indeliberately. Modern English was constructed from Middle English with various features changed to more closely represent the format of colloquial speech. Raku was constructed out of Perl in a similar way: various features were changed from Perl 5. The specific features that didn't pan out were described in the Raku ["apocalypses"](https://perl6.org/archive/doc/apocalypse.html).

So, Raku came to be as a solution to a problem, and the problem was that Perl 5 no longer aligned with what some Perl speakers wanted it to be<sup>[5]</sup>. Modern English came to be as a solution to the fact that Old and Middle English, at the very least, didn't represent their usages any longer as well. Where does that bring us? I'm not so sure.

Is a language worth it for the sake of its own existence? That's a question for a philosophist, not for me. But if you can't accept that Raku has a valid demographic, then at least consider that much.

To answer the question more directly: Raku's lack of a niche _is_ the language's niche. It appeals to functional programmers looking to dip their toes into something imperative, it appeals to low level programmers for having easy C interop, it appeals to array programmers for having extremely flexible containers and operators, etc, etc, etc. Raku allows the programmer to write code in their style that makes sense to them (see: baby Raku, as mentioned below) (also see: [https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml)).

Being able to choose your own niche is a strength all its own, and it allows Raku to appeal to each person and each problem in a unique way.

## Myth: Raku is inconsistent.

### Reality: [TMTOWTDI](https://en.wikipedia.org/wiki/There%27s_more_than_one_way_to_do_it). There's More Than One Way To Do It.

Language is inconsistent. Ideas are inconsistent. _Reality_ is inconsistent. Raku allows you to choose the best abstraction for the situation. That is the root of inconsistency -- and I don't feel like there's much more to say on that topic.

## Myth: Raku is too complicated and too hard to learn.

### Reality: Raku has layers of necessary complexity.

People who jump right into Raku immediately sink until the complexity piles up over their head. This complexity is necessary. I'm going to quote back to Damian Conway again: 

> One of the original Raku design philosophies was that complexity is intrinsically irreducible... but extrinsically redistributable

([from here](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml))

You can try as hard as you can to shift complexity away from new programmers, but it's impossible to stop them from seeking it out. If you show a new Raku programmer [this page](https://docs.perl6.org/language/operators), they will run away screaming in the other direction. That's why Raku is introduced slowly and softly -- using a little subset that the community likes to call "baby Raku". You can see an example of baby Raku on tutorial websites like [https://perl6intro.com/](https://perl6intro.com/) (the first tutorial listed at [https://perl6.org/resources/](https://perl6.org/resources/)). Only a handful of the operators are introduced, and hardly any of the control flow structures are introduced. The inner workings of the control flow structures aren't touched on at all! It doesn't even mention that `given/when` uses the smartmatch operator `~~` under the hood, which uses the `.ACCEPTS` method on the invocant under the hood.

This complexity is all there and it's all ready to be used. But if you're a beginner, or if you're just not planning on diving deep into the language, you have no reason to use it! It's all hidden from the programmer who doesn't want to get down and dirty with it. 

And that's what's important: the complexity is layered. You can use Raku using its surface level features and it'll function just fine as a Python-esq high level scripting language. But peel back those layers, and you're given the capability to do almost _anything_ that your heart may desire -- all the way down to modifying the very way that `nqp` parses the Raku language itself!

## Myth: Raku is hard to read.

### Reality: Badly written Raku is hard to read.

### Alternative reality: You cannot read a language which you do not know.

As a native English speaker who only speaks English y una lengua romance, I cannot read Chinese. I cannot even pretend to read Chinese. The symbolic lettering flies right over my puny Western mind. 

If you don't know Raku, it will fly right over your head. It is so dense with information that skipping a single symbol might drastically change your interpretation of the entire bit of code. Want to see how dense Raku can be? This is a automatically parallelizable map and fold on a list of numbers 1 to 100, using subroutines `f($x)` to map each number and `g($x, $accum)` to fold the list:

```
[[&g]] (1..100)».&f
```

Every single symbol in that line of code carries a significance, and the code would be drastically different should any of the symbols be missing or different. Imagine trying to read Chinese when you have to skim over every other symbol!

Problem is: there's definitely a limit to how much you should use these information-dense symbols. Admittedly, the precedence rules are confusing and the left & right binding seems to change willy-nilly. If you're from the land of Agda or Haskell you might learn to handle that<sup>[1]</sup>. If you're from the land of Java you might not. 

Good code and bad code is subjective, but in my subjective opinion: this could lead to bad code, and bad code is hard to read. With great power comes great responsibility.

## Myth: Raku is slow.

### Reality: You're trading off speed for expressiveness, but that speed deficit has been shrinking consistently.

Raku is an incredibly expressive language created by a tiny team of people. The priority was never speed, it was all-encompassing expressivity. You are purposefully making that trade off when you choose Raku. 

There are two bright sides though:

* Raku has _crazy_ good support for concurrency. The docs will do a better job than I could ever do explaining this, so here you go: [https://docs.perl6.org/language/concurrency](https://docs.perl6.org/language/concurrency). Another nice post about this is [here](http://blogs.perl.org/users/zoffix_znet/2016/04/perl-6-is-slower-than-my-fat-momma.html). It's an old article, but it's still applicable.

* Speed is consistently improving over in the land of the Rakudo compiler. [Look at all the open performance fixes waiting to be implemented in Rakudo](https://github.com/rakudo/rakudo/labels/performance)! There was a website that listed progressive benchmarks of the Rakudo compiler by version, but no matter how much Google-fu I slung I couldn't find it. I'd appreciate it if a reader who knows what page I'm talking about could submit it using the contact information below.

---

I know I've mentioned it a couple times now, but if you haven't read it you should go back and read [Raku is the World's Worst ML](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml). On top of that, I added a couple of extra new and insightful comments. Definitely worth it to check out! There are a couple more memorable quotes in there from Damien Conway. My favorite are the following paragraphs:

> I’m a big believer in languages adapting to the needs of the speaker, not vice versa. Just as there’s no one right way to speak in English, there should be no one right way to code in your preferred programming language. Most languages don’t really support that notion; many of them, in fact, are based on the diametrically opposite principle: “Everything has to be pure functional”, “Everything has to be objects”, “Everything has to be declared relationships”, “Everything has to be independent unreliable intercommunicating threads”. Each of which, in my opinion, is equivalent to: “Here’s your one true hammer. Now everything has to be a nail.”
> 
> Which, of course, serious developers reject. That means they now have to be fluent in Haskell and Erlang and Python and Node.js and a dozen other completely distinct and mutually incomprehensible development tools. And they have to continually context switch between them when writing and debugging and maintaining. And the vast majority of us just aren’t good at that.
> 
> That’s why I devoted two decades of my life to helping ensure that Raku lets you solve problems in whatever (reasonable) way that suits you…and suits your problem! By all means solve the majority of your problem functionally, but implement the inherently stateful parts of your program with objects, and handle the intrinsically linguistic components with declarative grammars. But do it all in the same language and at whatever granularity you are most comfortable with.

---

# Comments, corrections, and footnotes:

[/u/ogniloud on Reddit corrected a couple spelling errors across the post.](https://old.reddit.com/r/perl6/comments/ca47uy/perl_6_is_cursed_i_hate_it/et7bv33/)

[1] used to read "If you’re from the land of APL you might learn to handle that." An anonymous reader pointed out the fact that all APL operators are right associative, bind the same amount, and operation proceeds from right to left. 

[2] Section 1 used to read

> Raku is defined as a set of test cases that must be passed by any specific compiler. That test suite is here: [https://github.com/perl6/roast/](https://github.com/perl6/roast/). This test suite has been complete since the language's release, and thus so has the language. Raku's compilers may not implement the language in its entirety yet, but that does _not_ mean the language is incomplete.

A couple people, including [Rogue#2017 from the Raku discord](https://discord.gg/gg2a3T6) and [Timotimo](https://wakelift.de/) pointed out that this wasn't entirely accurate. Rakudo implements a _vast_ majority of this spec.

[3] This section was _awful_. It used to read 

> `Raku` is sometimes called `Raku` in order to distance it from `Perl 5`. `Raku`'s most popular compiler is `Rakudo Star`, which implements `Rakudo Raku`. `Raku` is built off of a language called `nqp`: Not Quite Perl. `Rakudo Star` uses a virtual machine called [`MoarVM`](https://www.moarvm.org/) which implements the virtual machine that `nqp` is compiled down to. `nqp` is then used to implement the majority of `Rakudo Star`. You read that right: the ubiquitous `Raku` compiler is implemented in a stripped down version of `Raku` itself. When you type `apt install perl6` (or whatever your equivalent is), your package manager will install `Rakudo Star`. `zef` is the `Raku` package manager. `Raku` packages live in `p6c` at [`http://modules.perl6.org/`](http://modules.perl6.org/). [`CPAN`](https://www.cpan.org/) DOES host Raku modules, and they are mirrored on the `p6c` website.

Additionally, it is inaccurate to say that `Rakudo Star` implements `Rakudo Raku`. `Rakudo Star` is the entire Raku packaging, including a few community packages ala Haskell Platform. `Rakudo` is the compiler. Most of what I wrote about `p6c` was also incorrect.

[4] This used to say "So what?", but an anomyous reader and a few people on HN suggested that this was an awful response to that myth. I agree. `ben509` on HN explained my response better than I feel like I ever could, though, so I supplanted his & the Raku IRC's explanation with mine. Also see [https://old.reddit.com/r/perl6/comments/ca47uy/perl_6_is_cursed_i_hate_it/et66gof/](https://old.reddit.com/r/perl6/comments/ca47uy/perl_6_is_cursed_i_hate_it/et66gof/).

[5] ~~Perl 5 wasn't a very good language~~

Did I miss any myths? Did I leave out an explanation that you like? Do you want to tell me how wrong I am? Please leave a comment using the contact info below! 