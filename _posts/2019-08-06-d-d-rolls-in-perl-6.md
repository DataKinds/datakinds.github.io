---
layout: post
title: D&D Rolls in Perl 6
date: 2019-08-06 17:33 -0700
---

Last week, I played my first D&D session with a couple of old friends from high school theatre. It was super fun and I haven't really gotten it off of my mind since.

I was reading up on the game, and I found out that the [dice notation that they use is actually rather intricate](https://en.wikipedia.org/wiki/Dice_notation). Might be old news for most of my audience, but it's new to me, and it's piqued my interest.

A basic dice roll in dice notation looks like `MdN`, where `M` is the number of dice to roll and `N` is the number of sides on the dice. So, something like `4d10` means "roll 4 dice with 10 sides".

I took that and defined a custom operator in Perl 6 to roll any number of n-sided dice. It looks like this:

```perl
sub infix:<d>(Int $n, Int $max) { (^$max).pick xx $n }
```

That operator can be called just as the notation would suggest.

```perl
say 3 d 20; 
# this rolls 3 dice with 20 sides
# when I ran it, I got 
# => (17 1 12)
```

If you just wanted the final outcome of the roll, it's easy enough to call `.sum` on the output of the roll.

```perl
say (3 d 20).sum;
# when I ran this, it gave me
# => 16
```

D&D lets you add modifiers to the dice rolls as well. You can multiply and add dice rolls or constants. For example, `2*(2d4+1)` will add 1 to the result of rolling 2d4, then multiply the whole roll by two.

This is super easy to replicate in Perl 6:

```perl
say 2 * ((2 d 4).sum + 1);
# gave me a result of 6
```

Even more exciting is the idea of _exploding dice_ or _open ended rolling_. Some games represent it as a postfix `!`, and it means "keep rolling and summing dice as long as you roll the max value".

For example, if you were to roll 4d20 and get `(1, 8, 20, 6)`, you'd add up the values 1, 8, 6 and 19 (it's not possible to roll a 20 with this method). Once you've added all those, you can roll the 20 again. Say you got a roll of `11`. Then, your total roll for `4d20!` would be 1 + 8 + 6 + 19 + 11 = 45.

To set that up in Perl 6, I created a new infix operator `b` (short for `b`ang, since the bang symbol `!` was taken). On the left side, it takes a list of dice rolls, and on the right side it takes the dice number to explode on. So, using it is a little more verbose than simply appending a `!` to your dice roll, but it's also more flexible and allows me to preserve the simplicity of the `d` operator doing nothing but producing a list of numbers.

```perl
sub infix:<b>(@ds, Int $max) is assoc<left> { 
    ( @ds, { .map: -> $d { $d ~~ ($max - 1) ?? |($d - 1, |(1 d $max)) !! $d } } ... * == * ).tail 
}
```

(thanks to [timotimo](https://wakelift.de/) for an idea of how to write this function!)

It looks complicated, but it's really just a lazy sequence that terminates when two elements are the same (that's what `... * == *` signifies). It starts with the list of dice rolls `@ds`, then iterates the function `{ .map: -> $d { $d ~~ ($max - 1) ?? |($d - 1, |(1 d $max)) !! $d }` until the dice list no longer changes. That function makes the dice explode: it maps the list of dice and appends new die rolls if it finds a die that's equal to the max roll.

It can be used as follows:

```perl
say 3 d 4 b 4;
# on my machine, my
# first roll got me
# => (2 0 0 2 1)
```

Another fun thing to do is to remove the `.tail` at the end of the definition of `b` to actually see how the dice explode over time:

```perl
sub infix:<b>(@ds, Int $max) is assoc<left> { ( @ds, { .map: -> $d { $d ~~ ($max - 1) ?? |($d - 1, |(1 d $max)) !! $d } } ... * == * ) }

say 2 d 3 b 3;
# my first roll was
# => ((2 1) (1 2 1) (1 1 1 1) (1 1 1 1))
```

The initial roll was `(2 1)`, but since we're rolling `d3`s labelled 0 through 2, every 2 we roll explodes.

After exploding the first dice in the first roll, we actually end up rolling another 2. Now our rolls total `(1 2 1)` and we can explode the 2 yet again.

Finally, we're left with `(1 1 1 1)`, our final roll.

Got any other cool ideas to implement and/or more fun facts about dice? Email or tweet me below.