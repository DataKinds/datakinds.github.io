---
layout: post
title: stacks, modules, oh my! a thinkbox (DRAFT!)
date: 2026-08-07 16:10 -0700
tags: thinkbox
---


ohhh baby, [i haven't done a thinkbox in a long time](https://datakinds.github.io/2019/06/30/jamq-thinkbox)!

quick note: i'll put a dagger symbol (†) next to any jargon i've made up, and i'll leave it out if i'm pretty sure the jargon is real. im trying to do a little novel classification here, so.

let me tell you about 

# modules

a module is a way to organize lots of behaviors under one scope. there are a few kinds of modules, and i want to suggest that these interpretations form a hierarchy. we're going to start with the narrowest end of the hierarchy and work our way up. and it'll be more relevant as we get to the ocaml section but we're discussing modules strictly as they exist dynamically, at runtime. no types here, sorry!

## static modules†

this is like what Haskell calls a module, or what many languages (Python, Kotlin, etc) will call a _package_.

```kotlin
package org.example

fun printMessage() { /*...*/ }
class Message(val text: String) { /*...*/ }
```

(from <https://kotlinlang.org/docs/packages.html>. okay, kotlin does have things it calls modules but it's the package that's more relevant here. kotlin's nominal modules are more strictly compilation units.)

these modules do nothing beside providing a scope and disambiguating names. so i can have a module which provides `org.example.PrintMessage` as above, but I can also have a secondary module which provides `org.example2.silly.PrintMessagee`. so let's quantify what this kind of module does for us:

* name disambiguation
* scoping and privitization
    * you can choose to not export a name from a module
* dependency demand†
    * you can import a module from another module, bringing its names into scope
    * notably you cannot do this arbitrarily, you MUST do this in an acyclic way in most languages, which will not do cycle analysis on this graph
* compilation unit separation
    * if a module hasn't changed, it doesn't need to be recompiled. this comes for free because of the scope boundary

## closure modules†

there is a middle-ground for how dynamic a module can be. instead of all data specified in a module being completely static, you can also have modules which close over some data explicitly and work with that data as state. 

a particularly interesting example exists in Maude's [system modules](https://maude.lcc.uma.es/maude-manual/maude-manualch5.html#x40-640005), in which entire rewriting theories can be scoped to a given module. a user may work with these modules as first class theory objects and may, for example, [do invariant checking directly on the module](https://maude.lcc.uma.es/maude-manual/maude-manualch11.html#x81-17400011).

modules as interpreted this way share a behavioral overlap with singleton classes. in a singleton class, we expect to only have one single instance (which may be statically identified by the class declaration), and this single instance can close over data and update itself. some singleton classes can even nest.

note that I'm NOT saying that these modules support implicit closure over some environment. we'll touch on that in the next section.

let's take a note of this capability:

* explicit closure
    * you can choose to store data within the module, and you can choose to come back and mutate that data

## polymorphic modules†

we've made the singleton class comparison, so let's extend that a little more. what if we could have N singleton classes, where each one was parameterized over a set of types and values?

i believe this to be equivalent in power (or actually just equivalent) to the sanitary macro. and as such let's talk about the C++ STL. check out this example:

```c++
template<typename T, T V>
class SingletonValue {
public:
    static T value() noexcept { return V; }
};
```
invoked as
```c++
auto x = SingletonValue<int, 42>::value();   // gives x == 42
auto y = SingletonValue<int, 69>::value();   // gives y == 69
```

in the jargon of the hierarchy we've built, `SingletonValue` IS a polymorphic module. it is parameterized over a set of types and/or values (we could have used `auto` to drop the typevar fwiw). it does all of the behaviors we've scoped out above, with the added benefit of also now allowing us to instantiate multiple of these singleton modules. so what extra behaviors does this get us?

* multiplicity
    * specifically, you may have 1 instance of a polymorphic module per unique parameter. so if you've got some sort of countable type, you've got infinite modules.

## functatorial (first-class) polymorphic modules

let's again expand the breadth of things a module can do---we now allow a closure module to take input. the only place i've ever seen this explicitly is in the SML family of languages. specifically, let's look at ocaml's [functors](https://ocaml.org/docs/functors):

> As suggested by the name, a functor is almost like a function. However, while the inputs and outputs of functions are values, functors are between modules. A functor has a module as a parameter and returns a module as a result. A functor in OCaml is a parametrised module, not to be confused with a functor in mathematics.

let me begin by saying that there is a rich mathematical backing to ocaml's unified module system, which comes equipped with a formal calculus based off [Rossberg's 1ML](https://people.mpi-sws.org/~rossberg/1ml/1ml.pdf). a little googling also [brought me to this paper](https://hal.science/hal-05428136/document), which speaks on a more recent ocaml feature (modular explicits) that allow higher-order functions to operate directly on modules and provides a calculus for unifying types quantified within modules with externally quantified types. 

PLEASE take a look at the 1ML paper, and especially the Related Work section, for an academic and historical perspective on this hierarchy of module behaviors. additionally if you're interested in the type-theoretic perspective and all the cool stuff ocaml can do with structs/model signatures, i'm not going to touch on that at all. I'm talking about modules from a purely dynamic perspective.

(now that i've said that, i can continue spewing colloquial bullshit)

the classic example of first-class functatorial modules in ocaml is a module that modifies another:

```ocaml
module type X = sig
  val x : int
end

module AddX (M : X) = struct
  let add y = M.x + y
end
```
(from <https://cs3110.github.io/textbook/chapters/modules/functors.html>)

note that this immediately gives us a few new capabilities to think about:

* nesting
    * modules can consume other modules as variables. passing in an anonymous module is identical to defining a module within another module in the effect it has on the environment

# who's janna?

[janna (she/her) is a VM for working with modules](https://github.com/DataKinds/continuation-playground). [in the tradition of naming my compilers after my OCs](https://github.com/DataKinds/tree-rewriter), so too is she named. she supports a number of actions on modules, and i'd like to take the time to quantify this set of actions so we can get a full idea of how to interact with a module.

janna is a fully dynamic VM and does not have an initial pass for things like type/effect checking. janna is built in the forth tradition of parsing words, so janna's not even statically decidable. this lets us do fun things with continuations without having to come up with a static managed runtime. if you want all these goodies with a type system on top of it, for Serious Work, go check out ocaml. 

the intention is that janna will support first-class polymorphic modules. she will be able to create these modules on the fly and do all the composing you'd like. so let's start walking up the hierarchy and look at the syntax and semantic of janna.

note that ALL the syntax and spellings here are subject to change, but it's valid as of 8/10/2026.

## quick sidenotes before we begin

### janna's output

i'll use double boxes for code input vs code output
```
this is input
```
```
and this is output
```

note that janna has a little debug printer that does its best to dump the VM state. this is what it looks like in a fresh VM, you'll see it again soon:

```
?
```
```
janna v0 knows 4 modules: io, debug, main, core
open scopes (active scope first): main

within main, there are 0 definitions:

within main, the resolution chain is: debug, io, core

within main, open stacks (active first): hole

within main, there are 1 stacks:
hole[]
```

### janna's datatypes

janna's [for a gamejam](https://itch.io/jam/catjam-2026) so she's only getting features on an as-needed basis. janna currently only has two types---terms, which are bare words; 
```
\ i'm-a-term .
```
```
i'm-a-term
```

and quotes, which are effectively sequences of words but stored internally as strings:
```
[ Hark! O'er the five seas, land emerges! ] .s
```
```
Hark! O'er the five seas, land emerges!
```

if i need something else i'll add that something else. integers are a good candidate. but without loops or conditionals i haven't seen the need yet!


## static modules

`enter:` is a syntax word that consumes one name term and enters the scope of the module given by the name. if the module doesn't exist, we create it. `leave` is its inverse, `:` is word definition. we call `?` twice so you, dear reader, can get Maximum Info, and see how janna is treating each scope here.

```
enter: foo
: bar [ hello world ] . ;
?
leave
[ -------- ] .s
?
```
```
janna v0 knows 5 modules: debug, main, core, foo, io
open scopes (active scope first): foo, main

within foo, there are 1 definitions:
bar

within foo, the resolution chain is: 

within foo, open stacks (active first): hole

within foo, there are 1 stacks:
hole[]

--------

janna v0 knows 5 modules: debug, main, core, foo, io
open scopes (active scope first): main

within main, there are 0 definitions:

within main, the resolution chain is: debug, io, core

within main, open stacks (active first): hole

within main, there are 1 stacks:
hole[]
```

### scoping, privitization, disambiguation

modules can hold named stacks and named definitions. this does a few things for us: firstly, disambiguation and scoping! watch us distinguish between two different definitions of `bar`:

```
enter: foo1
: bar [ hello world ] .s ;
leave
enter: foo2
: bar [ goodbye world ] .s ;
leave

enter: foo1 bar leave
enter: foo2 bar leave
bar
```
```
hello world

goodbye world

Unknown word bar in modules ["main"]
```

### dependency demand

### compilation unit separation

... is nonapplicable because we don't statically compile.

## closure modules

at initialization time, you're free to pull data from other names

## polymorphic modules

`enter` (not `enter:`) can take a quote as the name of the module. the first word from the quote is taken as the module name, all subsequent terms in the quote are taken as the parameter to the module.

TODO: maybe the subsequent words should be "the name of teh stack that we push the parameters to".... much to think about?

TODO alternate: this parameter is automatically populated onto a named stack within the module, `M.T`.

## first-class modules

TODO: there should be an anonymous version of `enter`, and `leave` should push the name of the module to the stack.

# janna's bag of tricks

special modules (like the parameterized module that holds continuations)