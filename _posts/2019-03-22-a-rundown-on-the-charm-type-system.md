---
layout: post
title: A Rundown on the Charm Type System
date: 2019-03-22 19:03 -0700
---

_This post was written on March 22nd, 2019. Many of the features shown here are either liable to change (especially implementation-wise) or have yet to be implemented._

[Charm](https://github.com/aearnus/charm) is a type safe [concatenative programming language](https://en.wikipedia.org/wiki/Concatenative_programming_language). It promises a lightweight, interoperable runtime, relative ease of use, and an extreme meta-programming capability.

Charm implements a basic type system which currently does not support inferred types. It supports the idea of [parametricity](https://en.wikipedia.org/wiki/Parametricity), in that functions working on generic types cannot change their functionality depending on the arguments passed in. There has been a large effort to make the Charm type system consistent -- both with itself, and at length with the [Curry-Howard correspondence](https://en.wikipedia.org/wiki/Curry%E2%80%93Howard_correspondence).

## A concrete example

At its core, Charm does work on a central stack. Data gets pushed onto the stack, and in return, data gets popped off of the stack. This is demonstrated by the type signature for the function `+`, shown below:

	+ : Num Num -> Num

Let's piece this apart. 

---
<br/>

	+ : ...

This is simply the declaration of the type of the function `+`. I opted to use the colon as the "has this type" operator, due to its relative cleanliness and connotation. `+ :` reads as if part of a list specifying what `+` is. Additionally, `:` is used by theorem proving languages as the type inclusion operator, and thusly has the same effect.

	... Num Num ...

In many functional programming languages, it is possible to [curry](https://en.wikipedia.org/wiki/Currying) the arguments of a function. In Charm, currying is unnecessary. It is replaced by the idea of stack passthrough. 

Imagine the function `+` operating on a stack containing the values `2, 3, 4`. `+`, as its name implies, adds the top two numbers off the stack. Thus, it will transform the stack `2, 3, 4` into the stack `5, 4`. This is interesting as it leaves the `4` on the bottom of the stack untouched! The value `4` "passed through" the `+` function unchanged, and can be used as an input to another function in the future. This has the exact same capabilites as traditional currying.

In Charm's type system, we denote that a function allows values to pass through using the first half of its type signature written above. `Num Num` on the left hand side of the arrow says that the function `+` only acts upon two numbers at the top of the stack and allows the rest of the stack to pass through unchanged.

	... -> Num
	
The symbol `->` denotes the application of the function. Unlike Haskell & company, a Charm type signature can only have one `->` since a function can only be applied once, and cannot be curried per-argument.

Finally, the `Num` to the right of the arrow denotes that after the function `+` has been applied, the `Num Num` that was previously on the top of the stack transforms to a singular `Num`.

## A less concrete example

Consider the valid Charm type signatures below.

	map : [a -> b] [a] -> [b]
	i : [a -> b] a -> b

These showcase a few important features of Charm's type system. 

The first type signature describes the quintessential `map` function. This shows the introduction of two type variables, `a` and `b`. The syntax for introducing these type variables is not set in stone as of yet, and may require something explicit such as a `forall a, b` declaration. This is because of an intricacy of how the Charm type system handles type variables. Just as you cannot prove implications whose consequent does not bear relation to the antecedent, you cannot introduce unbound type variables on the right side of the top level of a type signature. This is why I am considering the `forall` syntax: it would make this restriction on type variables explicit rather than implicit.

This first type signature shows another feature of the Charm type system: programs vs. homogeneous lists. A tenent of the Charm language is that programs are simply lists of functions and lists are inherently programs. This idea unfortunately breaks down when it comes to homogeneous arbitrary length lists. There is no type safe way to interpret a homogeneous arbitrary length list as a program without support for dependently typed lists who store their length in the type and variadic functions. Charm supports neither of these, so in lieu of these features, there is a special case for lists that contain only homogeneous elements. 

This special case is shown above: `[a]` or `[b]`. These refer to lists composed entirely of elements of type `-> a` or `-> b`, respectively. That is -- lists composed entirely of functions which do nothing but push a single value on the stack -- literals. Constrast that with the normal syntax for lists: `[a -> b]`. This represents a program (synonymous with a list) which pops `a` off of the top of the stack and pushes `b` onto the stack. 

Do note that `[a]` and `[-> a]` do not unify and thus are not equivalent type signatures. The former describes an arbitrary length list comprised entirely of `-> a`'s, whereas the latter describes a program whose entire action is to push a single `a` to the stack.

---

Consider the second type signature for the function `i`nterpret. This function interprets and executes the list `[a -> b]` as a program. It pops an `a` off the stack, and once executed, pushes a `b` back onto the stack.

It is very important to note the types of `a` and `b` in this situation (yes, the type of a type). In the first example, `Num` only represented one singular number on the stack. In every situation we've seen thus far, a type variable will only represent one singular type. However, this is not necessarily the case.

Type variables are entirely capable of being bound to multiple types at the same time. This means that the "type" of an arbitrary type variable is actually the non-empty list `[Type]`. Consider the following example as demonstration of this fact.

	2 3 4 5 # Push 2, 3, 4, 5 onto the stack. The stack now contains the types `Num Num Num Num`.
	[+ + +] # Push the program [+ + +] onto the stack. This has type `[Num Num Num Num -> Num]`.
	i       # Execute the program. `i` has type `[a -> b] a -> b`, so `a` becomes bound to the
	        # types `Num Num Num Num`, while `b` becomes bound to the type `Num`.
		# Therefore, the fully bound type signature of this instance of `i` becomes:
		# `[Num Num Num Num -> Num] Num Num Num Num -> Num`.

Take a second to ponder the type signature `[Num Num Num Num -> Num] Num Num Num Num -> Num`, and notice that it is exactly what we intended it to be.

## A more questionable example

Consider the following type signatures, involving "questionable" values.

	wrap : [a -> Bool] a -> a?
	maybe : b [a -> b] a? -> b
	lift : [a -> b] a? -> b?
	unwrap : [a -> b] [-> b] a? -> b
	
Astute readers will notice that this is very similar to Haskell's `Maybe` monad, and they'd be correct. Charm does not currently support higher kinded types and thus it cannot encode typeclasses, but `Maybe` is useful enough to encode as a builtin. A type which may or may not exist is denoted by a `?`, and takes up one spot on the stack regardless of whether or not the value actually exists. Values are promoted to these "questionable" values using the function `wrap`. Wrap pops a function and a value then makes a choice: if the function returned `True`, then it pushes the value; whereas if the function returned `False`, then it pushes `Nothing`. `Nothing` is a magical value which represents the lack of a value in a spot occupied by an `a?`. 

The type `a?` cannot unify with the type `a`. This forces the programmer to explicitly handle the case in which the questionable value is `Nothing`. The functions to handle these cases are `maybe`, `lift`, and `unwrap`. `maybe` is equivalent to Haskell's [maybe](https://hackage.haskell.org/package/base-4.12.0.0/docs/Prelude.html#v:maybe), and `lift` is equivalent to Haskell's [liftM](https://hackage.haskell.org/package/base-4.12.0.0/docs/Control-Monad.html#v:liftM). `unwrap` is left as an exercise to the reader :)

## A special note on type unification

Consider the `id` function. Its entire purpose is to return its input unchanged. In Charm's case, it should fufill the criteria that it does not mutate the stack at all. That is, it does not pop nor push a single value. Therefore its type signature is as follows.

	id : ->
	
This type signature says that `id` does not pop anything and does not push anything onto the stack. 

Consider Haskell's `id` function, though.

	id :: a -> a

In Haskell, `id` takes one argument and returns it unchanged. 

We can represent this same idea -- popping the top of the stack and pushing it back unchanged -- using the following type signature.

	id : a -> a
	
This has a strong implication underlying it: the type signature `->` is can unify with the type signature `a -> a`. By induction, this also means that the type signature `a -> a` would be equivalent to the type signature `a b -> a b`. This is in turn equivalent to the type signature `a b c -> a b c`.

Why is this, though? It is because of the idea of parametricity, mentioned above. If a function takes in an `a` but did not take in anything to do with `a` (such as an `[a -> b]`), the function _cannot_ modify the value of `a` in any way, shape, or form. 

There is one exception to `a -> a` being able to unify with `->` or `a b -> a b`. If the stack is empty before calling `id`, _only_ the type signature `->` will unify. `a -> a` and so forth require that there be at least one (or two, or three...) elements in the stack at the time of calling.

## Want to contribute?

All of the code for Charm is on GitHub at [https://github.com/aearnus/Charm]. Pull requests are readily accepted!
