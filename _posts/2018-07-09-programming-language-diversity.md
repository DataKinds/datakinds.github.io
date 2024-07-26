---
layout: post
title: Programming Language Diversity
date: 2018-07-09 16:25 -0700
categories: programming
---
A big topic in the study of ecology is the idea of biodiversity. Like everyone learned in middle school, it's the idea that an ecosystem needs a lot of different species to be healthy.

Programming languages also make up a sort-of ecosystem. Languages get born, they wax and wane, and they die out. Programming languages have "parents" (languages that they've taken features from) and "children" (languages that took features from them).

In my opinion, the programming language ecosystem is dying. There's such an overarching lack of diversity that the good ideas become drowned out by the popular ideas and paradigms.

Let's take a look at the StackOverflow 2018 survey from <https://insights.stackoverflow.com/survey/2018/>.

Here are the most popular programming languages from the "Most Popular Technologies" section of the survey (with all the non-language technologies pruned out):

* JavaScript: 69.8%
* Java: 45.3%
* Python: 38.8%
* C#: 34.4%
* PHP: 30.7%
* C++: 25.4%
* C: 23.0%
* TypeScript: 17.4%
* Ruby: 10.1%
* Swift: 8.1%
* Go: 7.1%
* Objective-C: 7.0%
* VB.NET: 6.7%
* R: 6.1%
* Matlab: 5.8%
* VBA: 4.9%
* Kotlin: 4.5%
* Scala: 4.4%
* Groovy: 4.3%
* Perl: 4.2%

There is only one primarily functional language in that list. There are many languages that treat functions as first class residents, like Python, C++, and Ruby. Scala is the only language there that places functional programming on the forefront. The overwhelming majority of languages there (JavaScript, Python, PHP, TypeScript, Ruby, Objective-C, VB.NET, R, VBA, and Perl) are imperative dynamic languages. The overwhelming homogeneity of that list almost seems to suggest that there's a "right" way to do things (at least in the corporate world). It's interesting to compare this list to the list of most loved programming languages, that shows many much more promising trends:

* Rust: 78.9%
* Kotlin: 75.1%
* Python: 68.0%
* TypeScript: 67.0%
* Go: 65.6%
* Swift: 65.1%
* JavaScript: 61.9%
* C#: 60.4%
* F#: 59.6%
* Clojure: 59.6%
* Scala: 58.5%
* Haskell: 53.6%
* Julia: 52.8%
* Java: 50.7%
* R: 49.4%
* Ruby: 47.4%
* Erlang: 47.2%
* C++: 46.7%
* Hack: 42.1%
* PHP: 41.6%
* Ocaml: 41.5%

I took a few languages out of this list just because they didn't quite fit (like Shell or HTML). There is a _lot_ more variety in these languages, between modern imperative languages like Rust, Lisps like Clojure, or fully functional languages like Haskell.

What does it say about the state of our little ecosystem that the popular languages are pretty much carbon copies of each other? It might be that they "just work", and that they're used _because_ they're similar, and thus they don't take as long to learn and it's not as hard to bring people onto a team using that language.

Here's the thing though: languages don't survive unless they're popular. A language may have some extremely good ideas, but just be dead in the water since nobody's interested in using it. Take Haskell, for example. Haskell's one of my favorite languages (and is loved by many, according to the survey), and I use it whenever possible. Its development is stagnant at best, though. At the end of the day, it's just not a popular language and thus it doesn't have the driving force behind it to continually improve it at the rate that other languages can continue to improve. C++, regardless of your opinion on it, does have that driving force behind it. People are vehement fans of that language on top of it being extremely popular. That is the kind of language that is going to outlive all of the Haskells and the Julias and the Hacks.

Common Lisp poses an interesting counter-example to all this. Despite being both unpopular and unused, it's been kept alive though a dedicated core following of fans and supporters. Somehow it's found its way into [large company's production code](https://tech.grammarly.com/blog/running-lisp-in-production) and the community has created pretty much [every facility you'd need in a common language](https://www.quicklisp.org/beta/). The compilers and interpreters for it are all continually updated: SBCL was last updated a month ago and CCL had its newest release 7 months ago. The Scheme standard (though a little off-topic) was last updated in 2017 with compilers and interpreters such as Chicken Scheme and Racket being updated day-to-day.

How have Common Lisp and its children stayed alive this long? A nice quote I found from lisp-lang.org says:

    Part of what makes Lisp distinctive is that it is designed to evolve. As new abstractions become popular (object-oriented programming, for example), it always turns out to be easy to implement them in Lisp. Like DNA, such a language does not go out of style.
    (Paul Graham)

Common Lisp first appeared in 1984, and has seen many an abstraction in its time. It's seen message passing, actors, OOP, functional style, futures, asynchronous code, massive concurrency, and so much more. Surprisingly, it's been able to keep up with all these changes in ideas. We can shoehorn the next big idea into JavaScript all we want, but it's hard to achieve true first class support like you could in a Lispy language.

So is extensibility the key to make a language that never dies? Well... maybe. It's hard to say without analyzing other languages just as extensible as Lisp. But, at the end of the say, I'm not so sure any other language like that exists.

Maybe the key to make a language that doesn't die is to not make a language at all. Lisp isn't a language; it's a set of syntax rules for interpreting and modifying [S-expressions](https://en.wikipedia.org/wiki/S-expression). Common Lisp is one implementation of those rules, as is Scheme and Racket and Clojure and the list goes on. Haskell's done this, too. GHC (the canonical Haskell compiler) is simply an implementation of a Haskell. There are many other implementations of it with different targets and different concepts, such as Elm, GHCJS, and Eta.

Maybe the key to making a language that doesn't die is a combination of the two. Or maybe C++ found the key already and we'll be stuck with it forever.

The surveys show the overwhelming homogeneity of the programming language ecosystem, but...

Why is it bad that programming languages die?
---
There are plenty of linguists who have written very good arguments for why languages shouldn't die, and I am not one of them. The gist of it is, though, that when a language dies, so do the ideas that it contained. Take [Forth](https://en.wikipedia.org/wiki/Forth_(programming_language)) from 1970, for example. It didn't have any children outside of defunct Forth derivatives like ColorForth. It was also the first language to make the distinction between functions that run at compile time and functions that run at run time. No language in the next decade or so had such comprehensive compile time tools until they were rediscovered in the form of C++ style templates (as far as Google would suggest. if this is incorrect please email me).

Here's an alternate scenario: Modula (1975) introduced the idea of coroutines and module based compilation units. These features have, in some capacity, stuck around in every single programming language where they'd make sense.

A language's death also means the death of the ideas the language contains. That's why the continuation of projects such as the [Wiki](http://wiki.c2.com/) or [Esolangs](https://esolangs.org/wiki/Main_Page) are so important: they prevent good ideas from dying out.

Are programming languages actually dying?
---
Yes, and it's because of this lack of diversity. Some languages, like Prolog, will dominate their niche until something better comes along. But, take Python for example. Depending on who you ask, Python is the quintessential dynamic scripting language. So, it's the language that gets all the funding and library development, while other languages (with much better ideas 😛) are left in the dust to die. Ruby and Perl/Raku, namely, are now considered dead languages that you shouldn't learn if you want a job in programming. Putting the fact that I'm a Ruby fanboy aside, this is awful because if these languages die, we lose ideas such as anonymous blocks, sigils, or full fledged PCRE.

Regardless of your opinion on non-Python scripting languages, losing them would be losing a large part of humanity's collective programming knowledge.

(I'm not a language researcher, but I'd like to hear from one on this topic. If you know more about this topic, please contact me through one of those methods down below. I'd love to talk about this!)

_If you liked this post, please [support me on Patreon](https://www.patreon.com/aearnus) or [become a patron on Liberapay](https://liberapay.com/Aearnus) to get more content in the future._
