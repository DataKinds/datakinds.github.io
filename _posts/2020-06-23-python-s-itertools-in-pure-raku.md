---
layout: post
title: Python's Itertools in Pure Raku
date: 2020-06-23 22:44 -0700
tags: raku
---

Python's [`itertools`](https://docs.python.org/3/library/itertools.html) package is the gold standard for working with iterable streams of data. 

It always irked me that Python needed an entire package to do things that I felt like the base language should easily support. I know Raku treats [lazy lists as first class objects](https://docs.raku.org/language/operators#index-entry-lazy_list_%E2%80%A6), so that made me start to wonder: how well does Raku stack up?

To answer this question, I'm just going to go through every function in the [`itertools`](https://docs.python.org/3/library/itertools.html) and provide a one liner Raku equivalent. These will all work with normal iterables as well as infinite lists and sequences. Let's begin:

[`count()`](https://docs.python.org/3/library/itertools.html#itertools.count):
```raku
-> $start, $end = * { $start ... $end }
```
---

[`cycle()`](https://docs.python.org/3/library/itertools.html#itertools.cycle):
```raku
-> @p { lazy gather { loop { for @p -> $p { take $p } } } }
```
From CIAvash on the Raku IRC:
```raku
-> @p { |@p xx * }
```

---

[`repeat()`](https://docs.python.org/3/library/itertools.html#itertools.repeat):
```raku
-> $elem, $n = * { $elem xx $n }
```

---

[`accumulate()`](https://docs.python.org/3/library/itertools.html#itertools.accumulate):
```raku
-> @p, &func = * + * { [\[&func]] @p }
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