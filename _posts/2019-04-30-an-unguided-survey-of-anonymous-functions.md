---
layout: post
title: An Unguided Survey of Anonymous Functions
date: 2019-04-30 00:15 -0700
---
<style type="text/css">
*, *:before, *:after {
	box-sizing: border-box;
}

.hvr {
	position: relative;
}

.cover {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: black;
	transition: all .5s;
}

.cover:hover {
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


<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Dyalog APL</h3>
</div>
```apl
8 {⍺+⍵} 7
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>J</h3>
</div>
```j
8 (+) 7
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Idris</h3>
</div>
```idris
(\ x y => x + y ) 8 7
(+) 8 7
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Coq</h3>
</div>
```
(fun x y => x + y) 8 7
(fun (x y : Int) => x + y) 8 7
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Haskell</h3>
</div>
```hs
(\x y -> x + y) 8 7
(+) 8 7
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>OCaml</h3>
</div>
```ocaml
(fun x y -> x + y) 8 7
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Emacs Lisp (Closure)</h3>
</div>
```elisp
(let ((lexical-binding t)) ((lambda (x y) (+ x y)) 8 7))
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Emacs Lisp (Lambda)</h3>
</div>
```elisp
(let ((x 8) (y 7)) ((lambda () (+ x y))))
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Joy</h3>
</div>
```joy
8 7 [+] i.
```


<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>ATS</h3>
</div>
```ats
(lam (x : int) (y : int) => x + y) 3 4
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Scala</h3>
</div>
```scala
(( _ :Int ) + ( _ :Int ))(8, 7)
((x:Int, y:Int) => x + y)(8, 7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Java 8</h3>
</div>
```java
((int x, int y) ->  x + y;)(8, 7);
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>C++ (Lambda)</h3>
</div>
```cpp
([](auto x, auto y) -> auto { return x + y; })(8, 7);
([](int x, int y) { return x + y; })(8, 7);
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>C++ (Closure)</h3>
</div>
```cpp
int x = 8; int y = 7; ([=]() { return x + y; })();
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Rust</h3>
</div>
```rust
(|x, y| { x + y })(8, 7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Raku</h3>
</div>
```p6
{ $^x + $^y }(8, 7);
( * + * )(8, 7);
(sub ($x,$y) { return $x + $y })(8,7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Javascript</h3>
</div>
```js
((x, y) => x + y)(8, 7);
(function(x, y) { return x + y })(8, 7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Crystal</h3>
</div>
```crystal
(->(x, y){ x + y }).call(8, 7)
(->(x : Int32, y : Int32){ x + y }).call(8, 7)
(Proc(Int32, Int32, Int32).new { |x, y| x + y}).call(8, 7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Nim</h3>
</div>
```nim
(proc (x: int, y: int): int = x + y)(8, 7)
(proc (x: int, y: int) = x + y)(8, 7)
import sugar; ((x: int, y: int) => x + y)(8, 7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Python</h3>
</div>
```py
(lambda x, y: x + y)(8, 7)
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Erlang</h3>
</div>
```erlang
(fun(X, Y) -> X + Y end)(8, 7).
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Ruby (Lambda)</h3>
</div>
```ruby
->(x,y){ x + y }[8, 7]
(Proc.new { |x, y| x + y })[8, 7]
(Kernel.lambda { |x, y| x + y })[8, 7]
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Ruby (Closure)</h3>
</div>
```ruby
method(def _(x, y) x + y end)[8, 7] # not a true anonymous function, yet worth including
```

<div class="hvr">
	<div class="cover">Hover to reveal.</div>
	<h3>Elixir</h3>
</div>
```elixir
(fn (x, y) -> x + y end).(8, 7)
(&(&1 + &2)).(8, 7)
```


---

This is by no means an exhaustive list, so if you have any comments or additions, please email me below.

---
Special thanks to [https://github.com/johnli0135/](https://github.com/johnli0135/ "https://github.com/johnli0135/") for providing a nonnegligible chunk of these examples.
