---
layout: post
title: Some Thoughts About Ruby and Smalltalk (OOP Thoughts part 2)
date: 2018-07-03 21:18 -0700
categories: programming ruby smalltalk
---

A Running List of Ruby Things I Didn't Know Came From Smalltalk
---
* Blocks (as a whole)
* Do blocks and Enumerator (the Ruby `Enumerator#each do ... end` common usage is referred to as  a loop object in Smalltalk)
* `Fixnum#upto`
* Using `#collect`, `#select`, `#reject`, and `#inject` as function names instead of the more pragmatic names `filter` or `fold`
* Using pipe characters for naming things (Ruby uses it for block arguments, Smalltalk uses it to declare variables)
* Having to call a function on a proc instead of just being able to call it like a normal function EVERY DAMN TIME (`#[]` or `#call` in Ruby vs `#value` in Smalltalk)
* Single quote strings
* Symbols
* Keyword arguments
* `#send`/`#__send__`
* `#function` syntax for documentation
* No boolean type: `true` is an instance of `TrueClass` and `false` is an instance of `FalseClass`
* Rational literals
* Automatic Fixnum -> Bignum conversion
* Asking an object if it `#respond[s]_to?` a function
* Class introspection (`#methods`, `#class`)
* Direct access to the GC & VM (the former is common in modern languages, but the latter is unique (and Ruby is only currently implementing it))
* Being able to monkey-patch classes on-the-fly, including modifying variable or function privacy
* Using `Class::initialize` as the function to initialize a class, even though you actually call it by saying `Class.new`
* The MVC model

A Running List of Smalltalk Things I Wish I Had in Ruby
---
* An IDE
* A _real_ debugger
* Enumerated lists of every instance of a class
* Source code browsing
    * Actually, I just kinda want [Pry](http://pryrepl.org/) to be the default instead of IRB.
* Operator chaining (`#tap` and `#yield_self` achieve this, but they're brand new and not widely supported)
* Built-in Ruby version control
    * RVM is an extra dependency and _should_ be unnecessary
* Variable pre-declaration

I'll continue updating this list as I continue learning.
