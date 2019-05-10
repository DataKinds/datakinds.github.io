---
layout: post
title: 'A Basis For Sequential Execution: Monads, Arrows, and More'
date: 2019-05-10 02:14 -0700
---

<script src='https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML' async></script>

(allergy notice: this page contains latex. If you have your Javascript disabled, please enable it for the best possible experience reading this blog post. I promise that's the only script on this site!)

---

On the first day, there was light.

On the second day, there was silicon.

And on the third (?) day, there was assembly.

Screens were overfilled with the grace of bytecode shorthand. Three letter opcodes were all the information that A Real Programmer would need. `jmp`, `cmp`, `mul`, `add`. Each translated byte would become an elegant haiku of the code being run. The entire world surrounding the code could have been described over a quick coffee at the watercooler.

Then, computers got bigger. They got faster, they got more complex. There became _too much_ for a single programmer to consider. Even a fleet of the world's best programmers couldn't write a triple A game in x86-64 assembly. Banks didn't like watching nerds with glasses twiddle their bits all day long.

So, the humble computer language came to be. First COBOL, then C, then Javapythonrubyc++phpjavascriptvba.

But mostly, C. C is, was, and will always be, a beautiful language <span style="font-size:0.4em;">to run on a PDP 11</span>. But as C became more widely (ab)used, it became increasingly apparent that C could be just as hard to reason with as the bit-twiddling lingua franca of yesteryear.

```c
static float CURSED_VALUE = 1.0f;
volatile const char* possibly_incorrect_format = "%d\n";

void effectual_func() {
	curse_of_the_elders:
	printf(possibly_incorrect_format, CURSED_VALUE);
	goto end;
}

int main() {
	goto curse_of_the_elders;
	end:
	return 0;
}
```

"But Tyler", you may ask. "Who would _ever_, in their right mind, write code like that?"

If you had the forethought to ask that question, you're probably an above average programmer. And that implies the existence of below average programmers who would happily write that code and go along with their day, unbeknownst to the eldritch horrors they've summoned as that tangled spaghetti web of code becomes slathered across multiple files and compilation units.

---

So, a few people came along and made programming make more sense. They birthed forth type systems and side effect management. They covered up the computer's inelegant internals just as rust may cover a metal. They made it possible to check if your code was safe before even letting it near an execution environment. They brought the best bits of math and computing closer than they'd ever been.

But, before we dive into all this, we must answer a question: what does it mean to run code?

---

There is a suprising amount of theory behind what it means to _compute_, to _run code_. There are plenty of different models of computing that one can use, such as [the finite state machine](https://en.wikipedia.org/wiki/Finite-state_machine), but by far the most common one is [the Turing machine.](https://en.wikipedia.org/wiki/Turing_machine).

Since this isn't really required theory for the rest of this post, we don't have to get into the mathematical weeds about what exactly a turing machine is. It will still be good to internalize it as we move forward, though. If this piques your curiosity, a link is provided above.

The gist is that a Turing machine is a hypothetical computing machine that has a "state register", a "state transition function", a "head", and a "tape".

The state of the machine is determined by its state transition function. The state transition function depends on what symbol is currently being read off of the tape, and has the ability to modify the state of the machine by moving the head, writing a symbol to the tape, or changing the state register.

This seems exceedingly arbitrary, but think of it this way: your computer can do only a very limited number of things. It reads a program from memory, and depending on how it interprets the instruction it is currently reading, it can:

* Modify the tape (read/write a value from memory)
* Move the head ([modify the `PC`/`IP`](https://en.wikipedia.org/wiki/Program_counter))
* Or, modify the state register (read/write a value from a CPU register)

The complexity comes from how we can combine these things. And, by God, it can get complex. So that's why it's incredibly useful to have some abstractions over this very raw theoretical model.

---

Now, we are finally ready to talk about "sequential execution". Sequential execution is exactly what it sounds like: executing one thing, then executing another. On a computer, this can be extremely messy, as there is no way to know how executing an arbitrary piece of code can effect the overall state of the computer. 

However, in mathematics, we have the concept of...

# Pure Functions

... which allow us to be able to represent the sequential execution of two pieces of code $$f$$ and $$g$$ as nothing more than $$f(g(x))$$ for some input $$x$$.

A _pure function_ is a function which does not modify state in any measurable way. I make the distiction of saying "measurable", because _anything_ that you do on a computer will affect the state of said computer. `add`ing numbers requires you to overwrite a CPU register, for example. But most programming languages allow you to abstract over these minute state modifications since the compiler is smart enough to handle them itself.

Let's consider the composition of two C functions: $$f$$ will add three to our input, and $$g$$ will take the square root of our input. This will be the last C that we'll write, so enjoy it while it lasts.

```c
#include <math.h>

float f(float x) {
	return x + 3.0;
}

float g(float x) {
	return sqrt(x);
}
```

Then, the composition of these two functions is represented as `f(g(x))`, such that `x` is a `float` which gets square root-ed, then gets `3.0` added to it.

Let's take this very simple example to introduce Haskell -- representing the future bases of sequential execution will be much, much easier if we use a language that's aware of how it executes things.

We'll define our same functions here, but this time in Haskell.

```hs
f x = x + 3.0

g x = sqrt x
```

(If you're already a Haskeller coming into this blog post, you most definitely won't enjoy the nonstandard formatting that I plan to use. I am doing this simply because I'm using Haskell as a vehicle to explain these ideas, not as a tool in and of itself.)

Now that we have these functions, we can make an observation. $$f(g(x))$$ is the same as applying the composition of $$f$$ and $$g$$ to $$x$$: $$(f \circ g)(x)$$.

Haskell understands this, and allows us to say exactly that. The sequential composition of `f` and `g` is `(f . g)(x)`.

---

Not every bit of code is as simple and self-contained as our hypothetical $$f$$s and $$g$$s. In order to get to the heart of execution, we need to be able to represent code that _does_ something. Haskell's type system enforces functional purity, so those functions `f` and `g` above _cannot_ do anything outside of simply returning their modified arguments else the compiler will reject them. We'll look at how Haskell chooses to deal with this criteria, but first, we'll look at the down and dirty method of handling program state.

# Continuations

Continutations are the bread and butter of control flow in Scheme and other similar Lisps. Unfortunately, I pronounce my fricatives quite clearly (ba dum tss...), so I can't really give code examples for using continuations.

The idea behind continuations, though, is treating the computer's state as a first class object. Then, using primitives such as `call-with-current-continuation`, the programmer can force other blocks of code into using certain continuations despite their written context. As far as methods for handling composition and sequential execution, using continuations directly is rather dirty because it requires manually juggling the state across every function called under continuations.

I may not speak Lisp-y, but I can read Lisp -- and Lisp code that overuses continuation passing is nearly as unreadable as the C example given at the top of the page. The best of the worst is still by no means good.

---

At the end of the day, though, continuations are quick and dirty but they _work_. Along with a conditional function, continuations give the programmer the power to derive every other bit of control flow. 

Because of the arbitrary jumping and context passing, continuations are very hard to represent under a strict type system. They're nigh impossible to represent in Haskell without a dedicated Continuation type, as there's no state to access from Haskell's pure-by-default functions anyway. To look at how Haskell gets around this, we must look at...

# Monads
# Arrows
# Applicatives
