---
layout: post
title: Rust Is Rarely The Right Choice
date: 2018-07-26 01:42 -0700
categories: programming
---

I tried really hard to give Rust a fair chance. I wrote my fair share of applications with it, ranging from little toy programs to (a few unfinished attempts at) at full fledged applications. For a span of about a month, it was my de facto programming language.

After all that, truth be told: I prefer C. For _almost every_ situation that one might decide to use Rust, C(++) is the better choice.

For a programmer working alone, Rust's pitfalls far outweigh its benefits. (More on why I say "a programmer working alone" later...)

Rust gets in your way
===
The borrow checker is a pain in the ass. I know why it's designed this way, and I admire the Rust devs' goals with it. If you aren't familiar with it, the gist is that Rust has extremely strict compile-time enforced scoping rules where "ownership" of variables get passed around scopes, deinitializing the variable automatically once it reaches the end of the final scope. It's not just a good idea, it's a _great_ idea. In fact, I'd venture to say that it's one of the best ideas CS has seen in the past decade. C++ wishes it had an RAII system this good. 

Just because it's good in theory, though, doesn't mean it's good in practice. A common point of contention with this system is **slices** (yuck!)

Skip this explanation if you already know what a slice is; but I need to explain it to be able to fully get across the absurdity that's coming up. 

I'll let the Rust docs do the rest of the talking. Take a second and read <https://doc.rust-lang.org/std/primitive.slice.html#method.split_at_mut>.

Ponder that for a moment. This is the _only_ way to access multiple areas of the same array at the same time -- even if the areas are completely independent. That's right: random access of an array using little slice windows is an idea that _fundamentally conflicts_ with Rust's idea of ownership. A single array object cannot have multiple pieces of code looking at arbitrary sections of it, even if those sections are guarenteed to be disjoint. Things such as [RwLock](https://doc.rust-lang.org/std/sync/struct.RwLock.html) can't even solve the problem of random slice access across threads. The programmer has to manually interface with the internals of whatever array object they're using (using `unsafe` mode) in order to provide a safe access interface.

This may sound like a niche issue, but I've run into this problem on _every single_ Rust program I've worked on. Sometimes, the solution is elegant, such as the DFT reconstruction algorithm I programmed [here](https://github.com/Aearnus/strange-probability-experiment/blob/master/src/main.rs#L150). Other times, though, this issue is a game changer. I've changed two whole projects over to C after dealing with this issue. The first project being something that dealt heavily with SDL2 audio, which uses pointers into structs and the like. The second project was a VM, where I wanted to have a thread constantly watching a slice of memory and interpret it as VRAM. This was the response I recieved to asking about that in a Rust discord server:

![As if incompatibility was the worst of my problems...](/assets/imgs/rust-is-not-the-right-choice/0.png)

All of this makes complete sense in theory. Borrowing semantics get super complicated (undecidable?) when you have tons of code paths looking at arbitrary pieces of memory. In practice, though, it only gets in the programmer's way. While it's a useful safety net to know for a fact when an array goes out of scope, it's not worth losing the utility of internal pointers (among many other things).

Rust has complexity creep
===
Rust is, by far, not a bloated language. The [standard library is small, modular, nicely documented, and easily browsable](https://doc.rust-lang.org/std/). The creep comes in when you look at how the language itself operates. The borrow checker, most of all, is a black box in the most literal sense of the word. It is nearly impossible for a Rust beginner to fully understand the implementation details of the borrow checker, and they get bit in the ass constantly because of it.

I don't think I need to write any more of this section after I link this blog post: <https://manishearth.github.io/blog/2015/05/27/wrapper-types-in-rust-choosing-your-guarantees/>. Or, maybe, this ["cheat sheet"](https://i.redd.it/moxxoeir7iqz.png) will explain it all?

There are tens of ways to get a reference to an object, all with slightly different semantics and slightly different behavior. This is _awful_, and it's a huge turn off for anyone who's just learning their `&`'s and their `&mut`'s.

Not everyone is a computer scientist, Rust, and even if your community is a shining beacon of what it means to be newcomer friendly, your language sure as hell isn't. Being one month in and barely being able to tell references apart is not a good sign for the complexity of your language.


So, what's good about Rust?
===
Rust makes the process of collaborating on a complex piece of software a breeze. Its strong compile time guarentees mean that in 99 cases out of 100, "if it compiles, it ships". You don't have to worry about others' code breaking and you don't have to worry about UB (eugh) or vague types in functions you didn't write. The language trades out friction between people and replaces it with friction between the user and the screen. It is a complex language so your programs don't have to be. In that way, it's a bit magical -- when you're working in a team. Alone, though, these benefits don't apply (or at least nowhere near in the capacity that they do while working with others). 

What should solo developers use instead?
===
I don't care, unless I'm using your software.

If you're making public facing software, though, a strong type system will _always_ make your code easier to reason about and easier to know that nothing Bad™ is happening. Depending on the project, maybe Rust will be the right tool. But it's way more likely that C/C++ will be the right tool (I'll nerd out about C++20 in some other blog post). It's also worth to consider that maybe the right tool could be something like [Pony](https://www.ponylang.org/), [ATS](http://www.ats-lang.org/), [Crystal](https://crystal-lang.org/), [Haskell](https://www.haskell.org/), or [Nim](https://nim-lang.org/) (slight bias towards the last two :P). Maybe the right tool isn't a language in this ol' staically compiled realm at all. Or maybe you're a JavaScript shill. To each their own.

It's more important to use the right tool for the job than it is to be personally attached -- and hell; if C is the right tool, there is no reason to shove Rust where it doesn't belong.
