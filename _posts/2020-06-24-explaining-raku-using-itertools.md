---
layout: post
title: Explaining Raku using Python's Itertools
date: 2020-06-24 19:20 -0700
---

A few days ago, I posted [Python's Itertools in Pure Raku](https://datakinds.github.io/2020/06/24/python-s-itertools-in-pure-raku) and I got [quite a few responses](https://www.reddit.com/r/rakulang/comments/heycja/pythons_itertools_in_pure_raku/fvuhz37/) [asking me to](https://www.reddit.com/r/rakulang/comments/heycja/pythons_itertools_in_pure_raku/fvvz8js/) [elaborate on these examples](https://www.reddit.com/r/rakulang/comments/heycja/pythons_itertools_in_pure_raku/fvvnvq2/). This page will then act as a useful addendum to the [Python to Raku nutshell](https://docs.raku.org/language/py-nutshell) page in the Raku docs.

Python's [`itertools`](https://docs.python.org/3/library/itertools.html) package is the gold standard for working with iterable streams of data. 

However, Raku treats [lazy lists as first class objects](https://docs.raku.org/language/operators#index-entry-lazy_list_%E2%80%A6), so that made me start to wonder: how well does the base Raku language stack up?

To answer this question, I'm just going to go through every function in the [`itertools`](https://docs.python.org/3/library/itertools.html) and provide a one liner Raku equivalent. These examples all work with normal [iterables](https://docs.raku.org/type/Iterator) as well as infinite lists and [sequences](https://docs.raku.org/type/Seq).

All of these examples will be presented as plain old [subroutines](https://docs.raku.org/type/Sub) -- Raku's equivalent of Python functions. Throughout this post, feel free to open a Raku interpreter by typing `raku` into your terminal and follow along. Let's begin:

# Count

[`count()` docs](https://docs.python.org/3/library/itertools.html#itertools.count).

`count()` takes two arguments: `start` and `end`. These are both numeric arguments, which are represented in Raku as [variables with the `$` sigil](https://docs.raku.org/language/variables#index-entry-sigil_$) because they only contain one value.

To define this function, let's start by defining a sub `count`:

```raku
sub count($start, $end = *) { ??? }
```

([`???`](https://docs.raku.org/language/operators#listop_???) is more or less Python's `pass`, to stub out stub code.)

First off, we give `$end` a default value of `*`. Default arguments work the same as they do in Python, however they are initialized every time the function is called instead of being computed once and cached as they are in Python. (You can see the documentation for that on the [Signature page](https://docs.raku.org/type/Signature#Optional_and_mandatory_arguments)).

The default value of `$end` here is where things get exciting. A bare `*` creates a [Whatever object](https://docs.raku.org/type/Whatever), which is a special value that many operators choose to interpret differently than other values. Let's finish the function and we'll see how it comes into play.

```raku
sub count($start, $end = *) {
    $start ... $end 
}
```

Raku, like Ruby if you're familiar, implicitly returns the final statement in of the subroutine.

The bulk of the logic happens in the [infix `...`](https://docs.raku.org/language/operators#infix_...) operator. This won't be the last time we see `...`, so it might be nice to let that those docs sink in for a minute.

In this situation, we use `...` to do two things: in the case that `$end` is provided, it simply creates a list from `$start` to `$end`. In the case that `$end` is not provided, it creates an infinite sequence from `$start` to whatever (`*`).

# Cycle

[`cycle()` docs](https://docs.python.org/3/library/itertools.html#itertools.cycle).

While `count()` took a scalar argument, `cycle()` takes a list which thusly must be wrapped using the [positional sigil `@`](https://docs.raku.org/language/variables#index-entry-sigil_@). It repeats this list infinitely. So, our subroutine definition will look like this:

```raku
sub cycle(@p) { ??? }
```

I've got two different ways of writing this subroutine: an explicit method and an implicit method.

## The explicit method

We're going to use Raku's [`gather / take` control flow structures](https://docs.raku.org/language/control#gather/take) to do this explicitly. `gather` tells Raku that the following block is going to generate a [sequence](https://docs.raku.org/type/Seq), and `take` yields a value in the sequence -- just like Python's `yield`.

```raku
sub cycle(@p) { 
    gather loop { 
        for @p -> $p {
            take $p
        }
    }
}
```

This definition should be rather readable to a Python programmer. A few things to pay attention to: the [`loop` control flow structure](https://docs.raku.org/language/control#loop) is just an infinite loop, and the [`for` control flow structure](https://docs.raku.org/language/control#for) used here is just like Python's `for ... in` construct.

## The implicit method

(Thank you to CIAvash on the Raku IRC!)

Here's where things get fun:

```raku
sub cycle(@p) { 
    |@p xx * 
}
```

There's a lot going on here, but in English, this reads like "concatenate (slip) infinite copies of `@p`". 

The <a href="https://docs.raku.org/language/operators#prefix_|">prefix <code class="highlighter-rouge">|</code></a> turns our `@p` into a [Slip](https://docs.raku.org/type/Slip), which is a class that automatically flattens the list that it's inserted into. The Raku-ism for concatenating two lists is to coerce them both into slips and create a new list out of those, which looks like: `|@a, |@b` for some lists `@a` and `@b`.

Once we've created the [Slip](https://docs.raku.org/type/Slip) from `@p`, we concatenate an infinite amount of them by passing whatever (`*`) to the list repetition operator [infix `xx`](https://docs.raku.org/language/operators#infix_xx).

# Repeat

[`repeat()` docs](https://docs.python.org/3/library/itertools.html#itertools.repeat).

We've actually already seen everything we need to create `repeat()`, so let's just do it!

```raku
sub repeat($elem, $n = *) {
    $elem xx $n
}
```

Here are some links if you need a refresher: [Whatever object (`*`)](https://docs.raku.org/type/Whatever), and [infix `xx`](https://docs.raku.org/language/operators#infix_xx).

# Accumulate

[`accumulate()` docs](https://docs.python.org/3/library/itertools.html#itertools.accumulate).

Accumulate is the first function that we've seen that takes a predicate function instead of a scalar or list-like value. To pass in a predicate function, we can use the [callable sigil `&`](https://docs.raku.org/language/variables#index-entry-sigil_&) to tell Raku that the argument we're passing in can be executed. Our subroutine signature's now going to look like this:

```raku
sub accumulate(@p, &func = * + *) { ??? }
```

This time, we're setting the default argument of `&func` to `* + *`. If you look at [Python's default argument for accumulate](https://docs.python.org/3/library/itertools.html#itertools.accumulate), you'll see that they're using a default argument of `operator.add`: a function which adds two values which are passed to it. 

If you've made the jump and guessed that somehow `* + *` is a function which takes two arguments and adds them together, you'd be 100% correct. Using whatever (`*`) in a statement actually coerces the entire statement to a [`WhateverCode` object](https://docs.raku.org/type/WhateverCode) and allows it to act as a function in its own right. If all these stars are making you see stars, the Perl 6 Advent Calendar blog has a [good post disambiguating all of them](https://perl6advent.wordpress.com/2017/12/11/all-the-stars-of-perl-6/).

Now that we understand `accumulate`'s signature, let's move on to the body of the function:

```raku
sub accumulate(@p, &func = * + *) {
    [\[&func]] @p
}
```

If you're an APL programmer, using `\` in an accumulator should be ringing a bell to you. This is a little bit simpler than it seems: `[ ]` is the [reduction metaoperator](https://docs.raku.org/language/operators#Reduction_metaoperators). In order to use a non-operator callable inside of it we must surround that callable with an extra pair of brackets, and in order accumulate intermediate results we use a `\` inside of the metaoperator itself. That's all that's going on here.

# Chain

[`chain()` / `chain.from_iterable()` docs](https://docs.python.org/3/library/itertools.html#itertools.chain).

This is the default behavior of [slurpy arguments](https://docs.raku.org/type/Signature#Types_of_slurpy_array_parameters).

```raku
sub chain(*@p) {
    @p
}
```

# Compress

[`compress()` docs](https://docs.python.org/3/library/itertools.html#itertools.compress).

This one might take a little bit to build up to, so let's take it step by step until we've built the whole function. The final product looks like this:

```raku
sub compress(@d, @s) {
    flat @d Zxx (+<<?<<@s)
}
```

Its operation is easy enough to explain in English. For every element in `@d`, we return it if its corresponding value in `@s` is truthy. Let's start with a much easier question. How do we tell which values in `@s` are truthy?

Raku has the [prefix `?` operator](https://docs.raku.org/language/operators#prefix_?) which coerces its argument to a boolean. The only problem is that it coerces the _whole_ argument, meaning it coerces lists to a single value:

```raku
> (0,1,2,3).WHAT
(List)
> ?(0,1,2,3)
True
```

In other words, we want to be able to coerce every element individually to a bool, not the whole thing. There are a couple ways to do this. We could use a classic for loop, we could use `map`, or we could use [hyper operators](https://docs.raku.org/language/operators#Hyper_operators). Just like [the reduction metaoperator `[ ]`](https://docs.raku.org/language/operators#Reduction_metaoperators) from before, you can make any operator into a hyper operator by using `<<` and `>>`. Let's see how this changes things:

```raku
> (0,1,2,3).WHAT
(List)
> ?<<(0,1,2,3)
(False True True True)
```

Aha! It's exactly what we want. Let's use the same trick to coerce them back to numbers, using the numeric context operator [prefix `+`](https://docs.raku.org/language/operators#prefix_+):

```raku
> +<<?<<(0,1,2,3)
(0 1 1 1)
```

Again, an APL programmer will see exactly where I'm going with this. Using the list we've created to replicate elements in `@s` will give us exactly what we want from `compress`. To do this, we can use the [zip metaoperator `Z`](https://docs.raku.org/language/operators#Zip_metaoperator) to pair off corresponding elements in each list automatically. Combining this with the list repetition operator [infix `xx`](https://docs.raku.org/language/operators#infix_xx) that we learned about earlier gets us very close to what we need:

```raku
> (0,1,2,3) Zxx (0,2,4,6)
(() (1 1) (2 2 2 2) (3 3 3 3 3 3))
```

Now we just have to flatten the final list with [`flat`](https://docs.raku.org/routine/flat):

```raku
> flat (0,1,2,3) Zxx (0,2,4,6)
(1 1 2 2 2 2 3 3 3 3 3 3)
```

And once we put the rest of the pieces together, we're done!

```raku
> flat (0,1,2,3) Zxx +<<?<<(0,2,4,6)
(1 2 3)
```

(Note: functional programmers may notice that we could have instead used a single call to [`flatmap`](https://docs.raku.org/routine/flatmap). If you give this a try, let me know 😉)

# Drop while

[`dropwhile()` docs](https://docs.python.org/3/library/itertools.html#itertools.dropwhile).

```raku
sub dropwhile(&pred, @seq) {
    gather for @seq {
        take $_ if (none &pred) ff *
    }
}
```

(Thanks to Rogue from the [Raku discord server](https://discord.gg/gg2a3T6) for a correction in this section.)

We've seen a lot of this before! Here are some refreshers if you need them: the [callable sigil `&`](https://docs.raku.org/language/variables#index-entry-sigil_&), the [`gather / take` control flow structures](https://docs.raku.org/language/control#gather/take), the [Whatever object (`*`)](https://docs.raku.org/type/Whatever).

That [`for` loop](https://docs.raku.org/language/control#index-entry-control_flow__for-for) looks a little bit different than the one we've already seen. It doesn't have a current iteration variable! That's like writing a Python loop `for value in list` as `for list`... which makes no sense in Python, but it makes perfect sense in Raku! Raku has a special variable called [`$_` which is called the "topic variable"](https://docs.raku.org/language/variables#index-entry-topic_variable). `$_` gets set to whatever you're currently talking about in your code -- in `for` loops it is the current loop variable, in `given` blocks it's the given variable, in [smartmatches](https://docs.raku.org/language/operators#infix_~~) it's the left hand side, etc, etc, etc.

So `dropwhile` simply says "take (and yield, if you will) the `$_` variable if `(none &pred) ff *` holds".

`(none &pred) ff *` uses two things you haven't seen before: the [`none` junction](https://docs.raku.org/type/Junction) and the flip-flop operator [infix `ff`](https://docs.raku.org/language/operators#infix_ff).

Junctions are another ball game entirely, and if you'd like to learn more about them, I wrote another blog post here: [GADTs and Superpositions in Raku](https://datakinds.github.io/2019/04/08/gadts-in-perl-6). The import gist here is that the `none` junction is only true if all of its constituents are false. Its one constituent is, in this case, the `&pred` callable.

Once `none &pred` returns True (meaning once `&pred` returns false), the flip-flop operator, well, flip-flops. By default, the flip-flop operator always returns False until its left side returns True, in which it'll return True until its right side returns True. It bounces back and forth between these two conditions forever.

We can override `ff`'s default functionality however by passing whatever (`*`) to the right side of it. This makes `ff` only flip-flop once and never again, returning True for the rest of time once the left side returns True.

# Filter false

[`filterfalse()` docs](https://docs.python.org/3/library/itertools.html#itertools.filterfalse).

This is a builtin: the [`grep` method, using a `none` Junction](https://docs.raku.org/type/List#routine_grep).

# Group by

[`groupby()` docs](https://docs.python.org/3/library/itertools.html#itertools.groupby).

This is a builtin: the [`categorize` method](https://docs.raku.org/type/List#routine_categorize) or the [`classify` method](https://docs.raku.org/type/List#routine_classify).

# Islice

[`islice()` docs](https://docs.python.org/3/library/itertools.html#itertools.islice).

This is a builtin: basic [positional list slices](https://docs.raku.org/type/List#routine_categorize) are capable of this.

# Starmap

[`starmap()` docs](https://docs.python.org/3/library/itertools.html#itertools.starmap).

```raku
sub starmap(&func, @seq) {
    @seq>>.&{ func(|$_) }
}
```

You've seen almost everything here except for the [methodop `.&` operator](https://docs.raku.org/language/operators#methodop_.&), allowing us to call our `{ func(|$_) }` [block](https://docs.raku.org/language/control#Blocks) as a method.

# Take while

[`takewhile()` docs](https://docs.python.org/3/library/itertools.html#itertools.takewhile).


```raku
sub takewhile(&pred, @seq) {
    |@seq ...^ { !pred($_) }
}
```

Some refreshers if you need them: the [infix `...^`](https://docs.raku.org/language/operators#infix_...) operator, the <a href="https://docs.raku.org/language/operators#prefix_|">prefix <code class="highlighter-rouge">|</code></a> operator, and the [`Block` object](https://docs.raku.org/type/Block).

# Tee

[`tee()`](https://docs.python.org/3/library/itertools.html#itertools.tee):

Not really sure that this one makes sense to implement, as we're technically working with lazy lists for the most part here and not generated sequences. 

For that matter, `Seq` does provide a builtin, the [`cache` method](https://docs.raku.org/type/Seq#(PositionalBindFailover)_method_cache), that may be used effectively the same way in practice.

# Zip longest

[`zip_longest()`](https://docs.python.org/3/library/itertools.html#itertools.zip_longest):

(todo)

# Product

[`product()`](https://docs.python.org/3/library/itertools.html#itertools.product):

```raku
sub product(+p) {
    [X] p
}
```

There's a few new things to introduce here. Before this, we used single star (`*@`) [slurpy arguments](https://docs.raku.org/type/Signature#Types_of_slurpy_array_parameters). I opted to use a [different kind of slurpy argument](https://docs.raku.org/type/Signature#index-entry-+_(Single_argument_rule_slurpy)) just to show that it exists. We then [reduce](https://docs.raku.org/language/operators#Reduction_metaoperators) our list `p` using the cross product operator [infix `X`](https://docs.raku.org/language/operators#infix_X) to create all of our cross products.

# Permutations

[`permutations()`](https://docs.python.org/3/library/itertools.html#itertools.permutations):

This is a builtin: the [`permutations` method](https://docs.raku.org/routine/permutations).

# Combinations

[`combinations()`](https://docs.python.org/3/library/itertools.html#itertools.combinations):

This is a builtin: the [`combinations` method](https://docs.raku.org/routine/combinations).

# Combinations with replacements

[`combinations_with_replacement()`](https://docs.python.org/3/library/itertools.html#itertools.combinations)

```raku
sub combinations_with_replacement(@p, $r) {
    |@p.combinations($r), |([Z] @p xx $r)
}
```

Left as an exercise to the reader 😊.

---

Well, that's about all of them. Every `itertools` function written on one page in pure Raku. Hope you enjoyed and maybe learned something!

Message me using the contact info below, if you'd like.

If you found this useful, why not [toss me a few bucks to support my blogging habit](https://paypal.me/tslimkemann)?