---
layout: post
title: 'Perl 6: 90% Syntax, 10% Magic'
date: 2018-08-03 18:28 -0700
---
I've spent the last few days learning Perl 6. As per the [website's request](https://perl6.org/community/), I'm writing something about my experience with it.  

First, a little bit about me: I come from a strong FP background, and I'm in love with Haskell, Ruby, and C++. I prefer strong, static type systems over weak, dynamic ones. I'm a [programming](https://aearnus.github.io/programming/2018/07/09/programming-language-diversity.html) [language](https://aearnus.github.io/programming/smalltalk/2018/07/03/some-thoughts-about-oop.html) [nerd](https://aearnus.github.io/programming/charm/2018/04/19/a-charming-proposal.html) and I love to experiment; if not just to see what I'm missing in my languages of choice.

# The First Impression

Perl 6 has [a type system](https://docs.perl6.org/language/typesystem). Perl 6 has [dependant types](https://docs.perl6.org/language/typesystem#subset). Perl 6 has [_**f e a r l e s s c o n c u r r e n c y**_](https://docs.perl6.org/language/operators#Hyper_operators). Perl 6 supports the creation of....

## The Inevitable Monad

```perl
role Monad[::A] { 
    only method bind((&f where (.signature.elems ~~ 1 and .returns ~~ :(::A))) --> ::A) { ... }
    only method wrap(::T --> Monad[::A]) { ... }
}
```

# Past The Point Of No Return

Perl 6 has some features that make me extremely jealous. To demonstrate these, I made a simple little Mandelbrot fractal renderer.

(From this point on, when I say Perl 6, I'm specifically referring to Rakudo Perl 6. Since this is the only maintained Perl 6 compiler in existence, they are essentially one and the same -- until another compiler comes along.)

While writing it, I encountered some absolutely amazing error messages. Perl 6 _knows_ what you're used to, and it corrects you. For example, I accidentally wrote `break` instead of the Perl 6-ism `last`. In response, it gave me this error message:

```
===SORRY!=== Error while compiling /home/tyler/scripts/mandelbrot.p6
Undeclared routines:
    break used at line 8. Did you mean 'last'?
    grid used at line 14
```

This back and forth of writing something I'm used to then being corrected by the Perl 6 compiler was surprisingly smooth. I wrote `.length` and it corrected me to `.elems`. It truly seems like the Rakudo Perl 6 maintainers thought about everything in the 18 (!!!) years this language has existed. Sadly, I encountered some things that I feel are complete deal breakers for Perl 6.

Before anything else, here's the code that I wrote:

```perl
sub f(Complex $z, Complex $c --> Complex) {
    $z ** 2 + $c;
}

sub apply_f(Complex $c --> Num) {
    my $z = $c;
    for ^50 {
        last if ($z = f($z, $c)).abs > 2;
    }
    $z.abs;
}

my @i= (-2, -1.99 ... 0.5);
my @j= (-1.5, -1.49 ... 1.5);
my @grid = (@i X @j).map: {Complex.new: |$_ };
my @map = @grid».&{apply_f $_};
for ^@i.elems -> $i_ {
    for ^@j.elems -> $j_ {
        print (@map[$j_ + $i_ * @j.elems] > 2 ?? "#" !! " ");
    }
    print "\n";
}
```

## Perl 6 is prohibitively slow

When I say prohibitively slow, I'm not kidding. It took `14.627` seconds to render a ≈300x300 image. A few seconds get shaved off by replacing the `»` with `.hyper.map`, bringing the time down to `8.708` seconds. But even then, Perl 6 failed to fully utilize all 4 of my CPU cores.

In comparison, I wrote this nearly identical program in Haskell:

```hs
import Data.Complex
import Data.List

f z c = (z ** 2) + c

main = 
    let grid = [[j :+ i | j <- [-2, -1.99 .. 0.5]] | i <- [-1.5, -1.49 .. 1.5]]
        grid' = (map . map) (\c -> last $ take 50 $ takeWhile (\z -> magnitude z < 4.0) $ iterate ((flip f) c) c) grid
    in
    putStrLn $ unlines $ ((map . map) (\z -> if magnitude z > 2.0 then '#' else ' ') grid')
```

To produce that same size image, it ran in _`1.849`_ seconds, with no multithreading, fully lazy linked lists, and no fiddling about with the runtime components. For shits and giggles, I rewrote it using the same level of multithreading and non-laziness that you'd see in the Perl 6 script.

```hs
import Data.Complex
import Data.List
import Data.Array.Repa

f z c = (z ** 2) + c

main = 
    let is = [-1.5, -1.49 .. 1.5] :: [Double]
        js = [-2, -1.99 .. 0.5] :: [Double]
        grid = fromListUnboxed (Z :. (length js) :. (length is)) [ j :+ i | j <- js, i <- is ] :: Array U DIM2 (Complex Double)
        grid' = (Data.Array.Repa.map) (\c -> last $ take 50 $ takeWhile (\z -> magnitude z < 4.0) $ iterate ((flip f) c) c) grid
    in
    putStrLn $ ((Data.List.map) (\z -> if magnitude z > 2.0 then '#' else ' ') (toList grid')) 
```

This brought the time down to a whopping `0.434` seconds. That's **20.06x** faster than the fastest time I could squeeze out of Perl 6. That time difference doesn't stem from Haskell being compiled, either. Using `runghc` (the Haskell interpreter), that program ran in `4.381` seconds, which is still around 2x faster than Perl 6 could manage.




