---
layout: post
title: GADTs and Superpositions in Raku
date: 2019-04-08 14:16 -0700
tags: raku
---

![Of course, this implies that comprehensibility and nonsense are equally likely, which is not true in the general case. Especially when working with Raku.](/assets/imgs/gadts-in-perl6/comic.png "Of course, this implies that comprehensibility and nonsense are equally likely, which is not true in the general case. Especially when working with Raku.")

<div style="width: 100%; text-align: center"><i>(comics made by the wonderful <a href="https://twitter.com/TristanBomb/">@TristanBomb</a>)</i></div>

---

In my [last post](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml "Raku is the World's Worst ML"), I talked about this bit of code for implementing a [Maybe ADT](https://en.wikibooks.org/wiki/Haskell/Understanding_monads/Maybe "Maybe monad") in Raku:

```perl
role Maybe[::A] { }
sub just(::A $x --> Maybe[::A]) { class Just does Maybe[::A] { has $.V = $x; }.new }
sub nothing(Any:U $t --> Maybe[$t]) { class Nothing does Maybe[$t] { }.new }
```

I knew this code was bad but at the time I couldn't figure out how to improve upon it. Rest easy, though, as Raku has us covered. Enter the [junction](https://docs.perl6.org/type/Junction "Junctions in Raku")....

A junction is a special type that represents a _superposition of eigenstates that collapse down to a single value in a boolean context_. In English, it's a type that is hand-crafted to match against values in special ways. Consider this simple conditional:

```perl
if 2 + 2 == 4 or 2 + 2 == 6 {
    say 'raku can do math!'
}
```

(Obviously 2 + 2 will never equal anything but 4, but in the case of some sort of reality bending apocalyptic event, we can never be too sure.)

This conditional can be rewritten using a junction as:

```perl
if 2 + 2 == 4|6 {
    say 'raku can do math /and/ collapse superpositions!'
}
```

In this case, the `==` operator _autothreads_ over the junction, effectively mapping each value to whether or not it equals `2 + 2`. We can see this by looking at this session:

```perl
trepl> 4|6             # This is simply the junction of values `4` and `6`.
==> any(4, 6)

trepl> 2 + 2 == 4|6    # This is autothreading over `4|6` using `==`.
==> any(True, False)

trepl> ?(2 + 2 == 4|6) # This is coercing that result to a boolean.
==> True
```

So, what does this have to do with GADTs? Or, perhaps more importantly...

# What is a GADT?

(tl;dr: [https://en.wikibooks.org/wiki/Haskell/GADT](https://en.wikibooks.org/wiki/Haskell/GADT "GADTs"))

GADT stands for "Generalized Algebraic Data Type", and it's essentially a highly generalized way to represent the constructors of a given type.

Consider the following Haskell snippet defining an extremely simple data type: `OurNullable` This type will work exactly the same as, say, `Nullable<T>` from Java -- thus managing to anger both Haskell programmers and Java programmers simultaneously. Our type can be constructed by either calling `OurNull` with no arguments or `OurFull` with one argument. It looks like this in Haskell: (do note that this is automorphic to the `Maybe` type)

```haskell
data OurNullable t = OurNull | OurFull t
```

But, what if we wanted to impose some arbitrary constraint onto the type we put in `OurNullable`? That's where GADTs come in. Let's say that instead, we only want values that we can call `==` on inside `OurNullable`. We would modify the `OurFull` constructor to only take in values which have this property. That looks like this:

```haskell
{-# LANGUAGE GADTs #-}

data OurEqNullable t where
    OurNull :: OurEqNullable t
    OurFull :: Eq t => t -> OurEqNullable t
```

Do note that we can also write the original version of `OurNullable` as a GADT as well:

```haskell
{-# LANGUAGE GADTs #-}

data OurNullable t where
    OurNull :: OurNullable t
    OurFull :: t -> OurNullable t
```

That's all you'll need to know about GADTs in order to appreciate the Raku heading your way.

# Putting it all together 
### Alternatively, _collapsing the eigenstates_

Now we're ready to represent `Maybe[::T]` as a GADT instead of a hacky role as we did at the top. For clarity's sake, we'll discard with this notion of `Maybe` and instead redefine `OurNullable`. Let me show you the whole thing before we break it down into pieces.

```perl
# Helper function
sub prefix:«>»($x) { $x.v; }

# Constructor 1
class OurNull { has $.t is required;
                method ACCEPTS($other) { $other.WHAT eqv OurNull and $other.t ~~ $!t } }
sub ourNull(Any:U $t) { OurNull.new: t => $t }
# Constructor 2
class OurFull { has $.v is required;
                method ACCEPTS($other) {
                    $other.WHAT eqv OurFull and do {
                        with $!v { >$other ~~ $!v.WHAT }
                        else { >$other ~~ $!v } } } }
sub ourFull($v) { OurFull.new: v => $v }
# Data declaration
sub IsNullable(Any:U $t) { ourNull($t) ^ ourFull($t) }

# Usage:
my $fill-it-up where IsNullable(Str) = ourFull('Raku rocks!');
my @possibly-list where IsNullable(Int) = ourNull(Int), ourFull(3), ourFull(5);

proto print-our-nullable($ where IsNullable(Any)) {*}
multi print-our-nullable(OurFull $m) { (>$m).say }
multi print-our-nullable(OurNull $)  { 'null'.say }

@possibly-list.map: &print-our-nullable;
```

First, we define a helper function `prefix:«>»($x)` at the top of the file. This isn't necessary per se, or even really necessary, but in my opinion it's nicer to have dedicated syntax for unwrapping values like this.

### Constructor 1: OurNull

```perl
class OurNull { has $.t is required;
                method ACCEPTS($other) { $other.WHAT eqv OurNull and $other.t ~~ $!t } }
sub ourNull(Any:U $t) { OurNull.new: t => $t }
```

This is the first constructor in our GADT, equivalent to the `OurNull :: OurNullable t` line in our Haskell implementation. We separate the type with its constructor by capitalization, so `OurNull` is the type which we construct using the routine `ourNull`. 

The `ACCEPTS` function is used during typechecking -- we only succeed in typechecking if we match against another instance of `OurNull` _and_ if the type that's contained within `OurNull` matches against the type passed in.

### Constructor 2: OurFull

```perl
# Constructor 2
class OurFull { has $.v is required;
                method ACCEPTS($other) {
                    $other.WHAT eqv OurFull and do {
                        with $!v { >$other ~~ $!v.WHAT }
                        else { >$other ~~ $!v } } } }
sub ourFull($v) { OurFull.new: v => $v }
```
Most of what is here is the same as before, with the exception of the `ACCEPTS` method. This time, we must have two branches related to the two scenarios which may end up calling `ACCEPTS`: when `OurFull` contains a type vs. when `OurFull` contains a value.

We use "undefined" instances of `OurFull` (ones that contain types instead of values) in the next section. In normal usage, most users will end up running into the latter case `>$other ~~ $!v`, in which case it returns the smartmatch of an unwrapped `OurFull` against an unwrapped version of itself.

### The GADT Junction

```perl
# Data declaration
sub IsNullable(Any:U $t) { ourNull($t) ^ ourFull($t) }
```

Here's where all the magic happens!

Finally, we've reached the `data OurNullable t where...` line. The reason I didn't call this routine `OurNullable` is because there's a fundamental difference in how we use this method versus how we'd use a normal type. `IsNullable` actually returns a _junction_ of `OurNull` and `OurFull` instances. 

When matching against this junction, the superpositions collapse into a truthy value (the match succeeds) if one and only one of the superpositions match, according to the `ACCEPTS` method. The superpositions collapse into a falsy value (the match fails) if any other case occurs.

So, this junction doesn't represent an actual type, it represents something we're going to match types against. Thus we must use a `where` block, which allows us to only accept a certain type if the type matches with a given predicate. 

### Usage

```perl
my $fill-it-up where IsNullable(Str) = ourFull('Raku rocks!');
my @possibly-list where IsNullable(Int) = ourNull(Int), ourFull(3), ourFull(5);

proto print-our-nullable($ where IsNullable(Any)) {*}
multi print-our-nullable(OurFull $m) { (>$m).say }
multi print-our-nullable(OurNull $)  { 'null'.say }

@possibly-list.map: &print-our-nullable;
```

The usage of `IsNullable` ends up being very similar to the usage of `Maybe[::T]`. The only caveat is that the `where` clause forces the typechecking of `IsNullable` types into runtime. This incurs a non-negligible overhead. For more on this, check out the previous article [Raku is the World's Worst ML](https://aearnus.github.io/2019/04/05/perl6-is-the-world-s-worst-ml "Raku is the World's Worst ML").

# Drawbacks

This method of handling GADTs is _slow_. For every runtime type check that occurs, `&IsNullable` must create an entire class for every constructor in the GADT. For this tiny little example, that's fine, but for an entire application it would become problematic.

After looking back at this implementation vs. the original one we've started with, I'm actually very disappointed at how this turned out. I thought that using junctions would simplify things -- but it merely ended up moving type checking to runtime. On the bright side, this movement of type checking to runtime means that we can further constrain the types we want to allow in our Nullable by constraining the type of `IsNullable`, but it's a small benefit for a huge cost. The only _real_ improvement that we've made over the original implementation is the introduction of the unwrapping operator `prefix:«>»($x)`, which is not specific to this implementation at all.

Nonetheless, there's enough valuable information here that I'm still excited to share it with you all. It's a really unique and flexible way of looking at problems -- one that's highly inspired by the functional, data-oriented paradigm.

---

Special thanks to guifa and AlexDaniel in the IRC (#perl6 on freenode) for talking through this a little bit with me.

![In some universe, this isn't the empty set.](/assets/imgs/gadts-in-perl6/comic1.png "In some universe, this isn't the empty set.")
