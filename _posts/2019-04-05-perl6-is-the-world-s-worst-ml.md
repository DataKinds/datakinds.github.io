---
layout: post
title: Perl6 is the World's Worst ML (with addendum by Damian Conway)
date: 2019-04-05 02:39 -0700
---

While reading through the docs for [Perl6's multi-dispatch](https://docs.perl6.org/language/functions#Multi-dispatch), I noticed something familiar: the language chooses which `multi` function to call depending on which routine's signature matches the argument first. Since Perl6 uses the smartmatch operator `~~` to match up type signatures, that means that we can also match on arguments by value....

10 minutes later, and I've created this abomination:

```perl6
proto len(@ --> Int) {*}
multi len([] --> 0) { }
multi len(@ [$, *@rest] --> Int) { 1 + samewith(@rest) }

say len <1 2 3 4 5 10>; # => 6
```

... equivalent to the much more readable Haskell version... 

```haskell
len :: [a] -> Integer
len [] = 0
len (_:rest) = 1 + len rest

main = print $ len [1, 2, 3, 4, 5, 10] -- => 6
```

---

The Perl6 code uses a few fun tricks that I'd like to point out. 

The `proto` declaration is entirely optional, just as the type signature in the Haskell version is entirely optional. I left them both in beacuse it's fun to compare the two versions.

The first `multi` declaration has the signature `([] --> 0)`. Therefore, this branch of the `len` routine is only called when the argument smartmatches against `[]`. When that smartmatch succeeds (when the argument is empty), `-->` bypasses the body of the function and instead returns a constant value (behavior documented [here](https://docs.perl6.org/type/Signature#index-entry---%3E "< --> > documentation")).

The second `multi` declaration has the signature `(@ [$, *@rest] --> Int)`. The first half of this signature pattern matches on a nonempty list: `@` is the positional sigil, which we leave anonymous since we don't need to use its value. `[$, *@rest]` is where the list deconstruction actually happens -- it matches against a single head value `$` which we leave anonymous, and slurps up the rest of the list into `@rest`.

Finally, the `1 + samewith(@rest)` line recurses through the list. `samewith` (documented [here](https://docs.perl6.org/language/functions#index-entry-dispatch_samewith "samewith documentation")) calls `len` with a new argument and the process repeats all over again, all the way down until we hit the base case.

---

No ML would be complete without some sort of abstract data type. Fortunately, we can emulate ADTs using classes and parametrized roles. Let's start by defining our `Maybe` ADT ([reference for the uninitiated](https://en.wikibooks.org/wiki/Haskell/Understanding_monads/Maybe "Haskell Maybe monad")).

```perl6
role Maybe[::A] { }
sub just(::A $x --> Maybe[::A]) { class Just does Maybe[::A] { has $.V = $x; }.new }
sub nothing(Any:U $t --> Maybe[$t]) { class Nothing does Maybe[$t] { }.new }
```

Here, we define `Maybe` as a parametric role. We define the constructors `just` and `nothing` as routines that return parametrized classes `Just` and `Nothing` respectively. Unfortunately, because Perl6 lacks any sort of meaningful type inference, the constructor for `Nothing` still needs to take in the uninhabited type which it will unify with as an argument.

Now, we can pattern match on the classes produced by our constructors to interact with our `Maybe` values.

```perl6
my @maybes = just(1), just(2), nothing(Int), just(4), nothing(Int);

proto print-maybe(Maybe $) {*}
multi print-maybe(Just $m) { $m.V.say }
multi print-maybe(Nothing $) { 'nothing'.say }

@maybes.map: { print-maybe $_ }
```

This produces:

```
1
2
nothing
4
nothing
```

And finally, compare this to the equivalent Haskell code:

```haskell
print_maybe :: Show a => Maybe a -> IO ()
print_maybe (Just m) = print m
print_maybe (Nothing) = putStrLn "nothing"
```

Sorry. Hope you enjoyed this :)

---

### Comments

Here's [Damian Conway, a Perl 6 language designer](http://damian.conway.org/About_us/Bio_formal.html), on the goals of the Perl 6 language in relation to this post:

> One of the original Perl 6 design philosophies was that complexity
> is intrinsically irreducible...but extrinsically redistributable.
> See: "The waterbed theory of language design".
>
> In line with that belief, Perl 6 almost always attempts to redistribute
> its syntactic complexity towards declarations...and away from calls.
> Mainly because declarations are typically written once, by a small
> number of expert developers, whereas calls are typically written many
> times, by a large herd of shall-we-say-less-than-expert developers.
>
> In other words, Perl tries to put the complexity where it will do
> the least harm, and where its victims may have the best chance
> of surviving it. :-)
>
> [snip]
>
> Of course, I'm not claiming that this high degree of flexibility is an
> unalloyed benefit (because there are plenty of plausible syntactic
> variations that wouldn't Do The Right Thing so obligingly).
> But in all those cases, the resulting error message is guaranteed to
> be clear and helpful, because any Perl 6 error message that is LTA
> ("Less Than Awesome") is officially considered to be a bug!
>
> The forgiving nature of Perl 6 syntax reflects several more
> of its fundamental design principles:
>
> 	* Perl 6 tries to do what you meant, not merely what your said.
> 	* Perl 6 has a long--but gentle--learning curve.
> 	* It's perfectly okay to write "baby Perl".
> 	* There's far more than one way to do it.
>
>
> Ultimately, I think that we're pretty happy with Perl 6 being the
> "World's Worst ML", so long as it can also (simultaneously) be:
> the "World's Worst Smalltalk", the "World's Worst Lisp",
> the "World's Worst Snobol", the "World's Worst QCL",
> the "World's Worst Erlang", the "World's Worst Prolog",
> the "World's Worst Python", the "World's Worst C",
> and even the "World's Worst Perl 5".
>
> Because that was one of our design goals too. :-)

He also offers this alternate, more readable definition of `len`:

	proto len(Positional --> Int) {*}
	multi len ([]) { 0 }
	multi len ([$, *@rest]) { 1 + len @rest }
	
	say len <1 2 3 4 5 10>;
	# or say len [1, 2, 3, 4, 5, 10];
	# or say len (1, 2, 3, 4, 5, 10);
	# or say len 1...5, 10;
	# or List.new(1,2,3,4,5,10).&len.say;
	
and argues that this is all just as readable (if not more) than the Haskell version. I'm inclined to agree.

---

Comments? Improvements? Ideas? Contact me below.
