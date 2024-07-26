---
layout: post
title: Strings as the first class citizen
date: 2019-10-02 20:53 -0700
---

I've been hard at work for the past few weeks at my new startup, [equalize digital](http://equalize.digital). But today, on a whim, I picked up Redis and was immediately awed at its flexibility. I knew I had to write a little something about it.

If you've never used Redis, it is an in memory database designed to speed up what would otherwise possibly be a traditional database request. Perhaps counterintuitively, especially for a database, Redis' only storable data type is the string. No numbers, no booleans, no `DateTime`s. Just strings and containers to place strings into. Redis' creator has [strong feelings on the topic](http://antirez.com/articoli/tclmisunderstood.html), and honestly -- this post is a sort of 13 year overdue response piece to that. Perhaps to see where the world is now.

# Stringy contexts

Out of every idea that people have had over the years, I think that the idea of the "stringy context" has caught on the most. I'll be talking specifically about Raku (it is explicit about its stringy contexts), but note that this applies to most every language that uses interpolation/coercion extensively (think Ruby, for example).

What I mean by "stringy context" is the idea that there are certain situations in which the language likes coercing things into strings. In Raku, you can create a stringy context by appending values with `~`:

```perl
~(1, 2, 3, 4) # this list of numbers becomes `1 2 3 4` in a stringy context
~[] # an empty array becomes `` in a stringy context
```

Sure, coercing things to strings isn't the most exciting thing in the world. But we're just getting started. 

Stringy contexts can be created in other ways, such as by interpolating things into double quoted strings:

```perl
my $favorite-number = 10;
"my favorite number is $favorite-number";
> 'my favorite number is 10'
```

This is _slick_, but it's not groundbreaking. The groundbreaking-ness comes from blurring the lines between what constitutes as a string and what constitutes as any other datatype. JavaScript was among the first real languages that tried this, but fell victim to its strange coercion rules and bad defaults. Let's take a look at how it works in Raku, through [the `Cool` class](https://docs.perl6.org/type/Cool):

```perl
# angle brackets create a list of strings
<1 2 3 3003 6006 9009> 
> (1 2 3 3003 6006 9009)

# adding 10 to each number
<1 2 3 3003 6006 9009>.map(* + 10) 
> (11 12 13 3013 6016 9019)

# running a regex on each number...?
<1 2 3 3003 6006 9009>.grep(* ~~ /00/) 
> (3003 6006 9009)
```

This comes in extremely handy.

# Strings as code

Antirez already said most of what there is to say about Tcl's manipulation of strings-as-code. It's sweet.

There's also a string-to-code isomorpism in Raku:

```perl
# some complicated type
my $c = Collation.new
> collation-level => 85, Country => International, Language => None, primary => 1, secondary => 1, tertiary => 1, quaternary => 1

# convert it to code
$c.perl
> Collation.new(collation-level => 85)

# and convert it back
EVAL $c.perl;
> collation-level => 85, Country => International, Language => None, primary => 1, secondary => 1, tertiary => 1, quaternary => 1
```

Jumping back a few decades, `m4` is one of my favorite examples of strings-as-code. It's old, an absolute pain in the ass to use, and super intimidating, but it's fun to consider at least as a pedagogical exercise. `m4` is a templating language originally used by `sendmail`, but now lives on in the form of autoconf scripts.

[Here's](https://mbreen.com/m4.html) a nice rundown of how `m4` works. The gist is that it's a macro expander on steroids.

Let's say I wanted to write some `m4` to produce a static HTML page. I might start with a generic HTML tag wrapper:

```m4
define(`tag', `<$1>$2</$1>')
```

And then I'd use that to define some wrappers: 

```m4
define(`tag', `<$1>$2</$1>')

define(`html', `tag(`ht`'ml', `$1')')
define(`body', `tag(`bo`'dy', `$1')')
```

A few things to note

* Quotes can be nested in `m4`. This is useful because text gets macro expanded in place where it's used, so quotes delay that expansion by one expansion step.

* Because `m4` eagerly expands text in place, the definition for the tags couldn't work without some janky business. There is an empty quote (\`') within the definitions of `html` and `body`, because if there wasn't, `m4` would think that it needs to expand the `html`/`body` macro in that place, and it'll loop forever.

All jankiness aside, this is interesting because the text _is_ the code. That's why the quoting rules are so odd -- they are the one construct that really seperate the text from being just plain text.

Let's finish this off with a demonstration of sorts.

```m4
define(`tag', `<$1>$2</$1>')

define(`html', `tag(`ht`'ml', `$1')')
define(`body', `tag(`bo`'dy', `$1')')

html(`body(`hello world!')')
```

outputs

```
<html><body>hello world!</body></html>
```

Boom. Strings as code. For better or for worse -- but if nothing else, as a useful situational tool.

# Other things like strings

* Raku's Plain Old Documentation is a very cool thing that is both a string and not a string.

* Lisp and the idea of lists as the first class object have been waxed poetic since time itself. I don't want to talk about that more -- everything that could have been said on that topic has been said.

* Arrays/matricies as the first class object are just as cool as strings, and I'd be happy to write another post on J/APL/A+/Raku/... in that regard.

* I don't know enough about Bash to have talked about it here. Perhaps a comment could fill in the gaps?