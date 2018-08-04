---
layout: post
title: 'Perl 6: 90% Syntax, 10% Magic'
date: 2018-08-03 18:28 -0700
---
I've spent the last few days learning Perl 6. As per the [website's request](https://perl6.org/community/), I'm writing something about my experience with it.  

First, a little bit about me: I come from a strong FP background, and I'm in love with Haskell, Ruby, and C++. I prefer strong, static type systems over weak, dynamic ones. I'm a [programming](https://aearnus.github.io/programming/2018/07/09/programming-language-diversity.html) [language](https://aearnus.github.io/programming/smalltalk/2018/07/03/some-thoughts-about-oop.html) [nerd](https://aearnus.github.io/programming/charm/2018/04/19/a-charming-proposal.html) and I love to experiment; if not just to see what I'm missing in my languages of choice.

The First Impression
===
Perl 6 has [a type system](https://docs.perl6.org/language/typesystem). Perl 6 has [dependant types](https://docs.perl6.org/language/typesystem#subset). Perl 6 has [_**f e a r l e s s c o n c u r r e n c y**_](https://docs.perl6.org/language/operators#Hyper_operators). Perl 6 supports the creation of....

### The Inevitable Monad

```perl6
role Monad[::A] { 
    only method bind((&f where (.signature.elems ~~ 1 and .returns ~~ :(::A))) --> ::A) { ... }
    only method wrap(::T --> Monad[::A]) { ... }
}
```

Past The Point Of No Return
===
Perl 6 has some features that make me extremely jealous. To demonstrate these, I'm going to made a simple escape time fractal renderer.

While writing it, I encountered some absolutely amazing error messages. Perl 6 _knows_ what you're used to, and it corrects you. For example, I accidentally wrote `break` instead of the Perl 6-ism `last`. In response, it gave me this error message:

```
===SORRY!=== Error while compiling /home/tyler/scripts/mandelbrot.p6
Undeclared routines:
    break used at line 8. Did you mean 'last'?
    grid used at line 14
```

This back and forth of writing something I'm used to then being corrected by the Perl 6 compiler was surprisingly smooth. I wrote `.length` and it corrected me to `.elems`. It truly seems like the Rakudo Perl 6 maintainers thought about everything in the 18 (!!!) years this language has existed.