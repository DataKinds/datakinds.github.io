---
layout: post
title: An Unguided Survey of Anonymous Functions
date: 2019-04-30 00:15 -0700
---
<style type="text/css">
*, *:before, *:after {
	box-sizing: border-box;
}

.hvrer::before {
	content: "Hover to reveal.";
}

.hvrer {
	background-color: black;
	transition: all .5s;
	color: white;
	width: 100%;
	height: 3em;
	margin: 0;
	margin-top: -3.5em;
	margin-bottom: 0.5em;
	padding: 0.5em;
}

.hvrer:hover {
	opacity: 0;
}

h3 {
	margin-top: 2em;
}
</style>

There is beauty in all forms of intentional design. Programming languages are no exception. 

Presented below is a collection of anonymous functions from various programming languages. Some languages provide the ability to form both _closures_, anonymous functions which save the context they were created in; and _lambdas_, anonymous functions which run under the context they are called from. Languages which provide both facilities are noted as seperate entries. Otherwise, it is glossed over. Seperate implementations will be kept on seperate lines.

Every function here is semantically identical: take two numbers and add them together. Such a simple function was chosen to place the focus on the function's structure on the page over the ease/hassle of writing it. Once the function is created, we call it with the two arguments 8 and 7. 8 was chosen as it is my favorite number. 7 is lucky. Their sum, 15, is undisputable.

This menagerie is not in any specific order, though care was taken to keep similar languages clumped together. The names of the languages are hidden to encourage you to ponder the code sample before revealing what language the code is from. A rose by any other name may be sweeter than you thought. 

<br/><br/><br/><br/>
<br/><br/><br/><br/>
<br/><br/><br/><br/>
This space was intentionally left blank.
<br/><br/><br/><br/>
<br/><br/><br/><br/>
<br/><br/><br/><br/>


### Idris
<div class="hvrer"></div>
```idris
(\ x y => x + y ) 8 7
(+) 8 7
```

### Coq
<div class="hvrer"></div>
```
(fun x y => x + y) 8 7
(fun (x y : Int) => x + y) 8 7
```

### Haskell
<div class="hvrer"></div>
```hs
(\x y -> x + y) 8 7
(+) 8 7
```

### OCaml
<div class="hvrer"></div>
```ocaml
(fun x y -> x + y) 8 7
```

### Emacs Lisp (Closure)
<div class="hvrer"></div>
```elisp
(let ((lexical-binding t)) ((lambda (x y) (+ x y)) 8 7))
```

### Emacs Lisp (Lambda)
<div class="hvrer"></div>
```elisp
(let ((x 8) (y 7)) ((lambda () (+ x y))))
```

### Joy
<div class="hvrer"></div>
```joy
8 7 [+] i.
```


### ATS
<div class="hvrer"></div>
```ats
(lam (x : int) (y : int) => x + y) 3 4
```

### Scala
<div class="hvrer"></div>
```scala
(:(Int, Int) => Int = _ + _)(8, 7)
(:(x:Int, y:Int) => Int = x + y)(8, 7)
(( _ :Int ) + ( _ :Int ))(8, 7)
((x:Int, y:Int) => x + y)(8, 7)
```

### Java 8
<div class="hvrer"></div>
```java
((int x, int y) ->  x + y;)(8, 7);
```

### C++ (Lambda)
<div class="hvrer"></div>
```cpp
([](auto x, auto y) -> auto { return x + y; })(8, 7);
([](int x, int y) { return x + y; })(8, 7);
```

### C++ (Closure)
<div class="hvrer"></div>
```cpp
int x = 8; int y = 7; ([=](int x, int y) { return x + y; })();
```

### Rust
<div class="hvrer"></div>
```rust
(|x, y| { x + y })(8, 7)
```

### Perl 6
<div class="hvrer"></div>
```p6
{ $^x + $^y }(8, 7);
( * + * )(8, 7);
(sub ($x,$y) { return $x + $y })(8,7)
```

### Javascript
<div class="hvrer"></div>
```js
((x, y) => x + y)(8, 7);
(function(x, y) { return x + y })(8, 7)
```

### Crystal
<div class="hvrer"></div>
```crystal
(->(x, y){ x + y }).call(8, 7)
(->(x : Int32, y : Int32){ x + y }).call(8, 7)
(Proc(Int32, Int32, Int32).new { |x, y| x + y}).call(8, 7)
```

### Nim
<div class="hvrer"></div>
```nim
(proc (x: int, y: int): int = x + y)(8, 7)
(proc (x: int, y: int) = x + y)(8, 7)
import sugar; ((x: int, y: int) => x + y)(8, 7)
```

### Python
<div class="hvrer"></div>
```py
(lambda x, y: x + y)(8, 7)
```

### Erlang
<div class="hvrer"></div>
```erlang
(fun(X, Y) -> X + Y end)(8, 7).
```

### Ruby (Lambda)
<div class="hvrer"></div>
```ruby
->(x,y){ x + y }[8, 7]
(Proc.new { |x, y| x + y })[8, 7]
(Kernel.lambda { |x, y| x + y })[8, 7]
```

### Ruby (Closure)
<div class="hvrer"></div>
```ruby
method(def _(x, y) x + y end)[8, 7] # not a true anonymous function, yet worth including
```

### Elixir
<div class="hvrer"></div>
```elixir
(fn (x, y) -> x + y end).(8, 7)
(&(&1 + &2)).(8, 7)
```


---

This is by no means an exhaustive list, so if you have any comments or additions, please email me below.

---
Special thanks to [https://github.com/johnli0135/](https://github.com/johnli0135/ "https://github.com/johnli0135/") for providing a nonnegligible chunk of these examples.
