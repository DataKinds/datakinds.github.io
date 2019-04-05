---
layout: post
title: Perl6 is the World's Worst ML
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

Here, we define `Maybe` as a parametric role that stores the type that it contains in an instance variable `$!T`. We define the constructors `just` and `nothing` as routines that return parametrized classes `Just` and `Nothing` respectively. Unfortunately, because Perl6 lacks any sort of meaningful type inference, the constructor for `Nothing` still needs to take in the uninhabited type which it will unify with as an argument.

Now, we can use our `Maybe` in the most Perl6 way possible: with `when` blocks.

```perl6
my @maybes = just(1), just(2), nothing(Int), just(4), nothing(Int);
for @maybes -> $maybe {
	given $maybe.WHAT {
		when Just {
			$maybe.V.say;
		}
		when Nothing {
			'nothing'.say;
		}
	}
}
```

Sorry.

---

Comments? Improvements? Ideas? Contact me below.
