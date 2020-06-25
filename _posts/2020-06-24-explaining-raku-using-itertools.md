---
layout: post
title: Explaining Raku using Python's Itertools
date: 2020-06-24 19:20 -0700
---

# WORK IN PROGRESS
# WORK IN PROGRESS
# WORK IN PROGRESS
# WORK IN PROGRESS
# WORK IN PROGRESS

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

The [prefix `|`](https://docs.raku.org/language/operators#prefix_|) turns our `@p` into a [Slip](https://docs.raku.org/type/Slip), which is a class that automatically flattens the list that it's inserted into. The Raku-ism for concatenating two lists is to coerce them both into slips and create a new list out of those, which looks like: `|@a, |@b` for some lists `@a` and `@b`.

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

```raku
sub accumulate(@p, &func = * + *) { [\[&func]] @p }
```

---

[`chain() / chain.from_iterable()`](https://docs.python.org/3/library/itertools.html#itertools.chain):

This is the default behavior of [slurpy arguments](https://docs.raku.org/type/Signature#Types_of_slurpy_array_parameters).
```raku
-> *@p { @p }
```

---

[`compress()`](https://docs.python.org/3/library/itertools.html#itertools.compress):

```raku
-> @d, @s { flat @d Zxx @s }
```

---

[`dropwhile()`](https://docs.python.org/3/library/itertools.html#itertools.dropwhile):

```raku
-> &pred, @seq { lazy gather for @seq { take $_ if &pred ff * } }
```

---

[`filterfalse()`](https://docs.python.org/3/library/itertools.html#itertools.filterfalse):

This is a builtin: the [`grep` method, using a `none` Junction](https://docs.raku.org/type/List#routine_grep).

---

[`groupby()`](https://docs.python.org/3/library/itertools.html#itertools.groupby):

This is a builtin: the [`categorize` method](https://docs.raku.org/type/List#routine_categorize) or the [`classify` method](https://docs.raku.org/type/List#routine_classify).

---

[`islice()`](https://docs.python.org/3/library/itertools.html#itertools.islice):

This is a builtin: basic [positional list slices](https://docs.raku.org/type/List#routine_categorize) are capable of this.

---

[`starmap()`](https://docs.python.org/3/library/itertools.html#itertools.starmap):

```raku
-> &func, @seq { @seq>>.&{ func(|$_) } }
```

---

[`takewhile()`](https://docs.python.org/3/library/itertools.html#itertools.takewhile):

I could write this the same as `dropwhile()` but just break out of the `for` loop early, but I'm gonna take full advantage of the [sequence operator](https://docs.raku.org/language/operators#infix_...) here instead.
```raku
-> &pred, @seq { |@seq ...^ { !pred($_) } }
```

---

[`tee()`](https://docs.python.org/3/library/itertools.html#itertools.tee):

Not really sure that this one makes sense to implement, as we're technically working with lazy lists for the most part here and not generated sequences. 

For that matter, `Seq` does provide a builtin, the [`cache` method](https://docs.raku.org/type/Seq#(PositionalBindFailover)_method_cache), that may be used effectively the same way in practice.

---

[`zip_longest()`](https://docs.python.org/3/library/itertools.html#itertools.zip_longest):

Probably the hardest one to implement. Nothing in Raku really naturally does this operation. Without accounting for length, and stopping at the shortest list, it would simply be:
```raku
-> **@p { [Z] @p }
```
Going to think about this one overnight, actually. I feel like there's an elegant way to do this really quickly but I can't put my finger on it. Pester me using the links down below if I haven't filled this one in yet.

---

[`product()`](https://docs.python.org/3/library/itertools.html#itertools.product):

```raku
-> +p { [X] p }
```

---

[`permutations()`](https://docs.python.org/3/library/itertools.html#itertools.permutations):

This is a builtin: the [`permutations` method](https://docs.raku.org/routine/permutations).

---

[`combinations()`](https://docs.python.org/3/library/itertools.html#itertools.combinations):

This is a builtin: the [`combinations` method](https://docs.raku.org/routine/combinations).

---

[`combinations_with_replacement()`](https://docs.python.org/3/library/itertools.html#itertools.combinations):

```raku
-> @p, $r { |@p.combinations($r), |([Z] @p xx $r) }
```

---

Well, that's about all of them. Every `itertools` function written on one page, in Raku one liners. This is all just food for thought: there really is no reason for Python to be as verbose and yet so lacking in features, and I suppose this is some sort of proof.

Message me using the contact info below, if you'd like.