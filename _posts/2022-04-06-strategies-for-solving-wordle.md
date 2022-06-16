---
layout: post
title: Considerations in Solving Wordle
date: 2022-04-06 11:29 -0700
tags: raku
---

The other day, my partner and I were competing to see who could solve the day's Wordle the quickest. She got it in 4 guesses. I... didn't get it after all 6 guesses. In an effort to not repeat that kind of humiliation, I wrote a program to help you narrow down your Wordle guesses based on the information that the game gives you after every guess.

If you haven't seen 3b1b's excellent video series on Wordle and information theory, [watch it first](). The only reason I found this problem interesting at all was because 3b1b made it interesting. One thing that bothered me about the video, though, was that he used the Wordle wordlist directly. I feel like that goes against the spirit of the problem, regardless of what he said.

# Shut up and give me the code!

Ok. You need a 2022 version of Rakudo Star to run it. (And you don't have to be so rude!)

```
#!/usr/bin/env raku

subset Guess of Str where * ~~ / ^ \w**5 \, <[012]>**5 $ /;

sub MAIN(
  Str :wl(:$wordlist) where *.IO.f = '/usr/share/dict/words', #= The wordlist
  *@guess where .all ~~ Guess #= Wordle guesses, given as space-delimited command line arguments. Each guess is of the form <word>,<feedback> where <word> is the guessed word and <feedback> is 0, 1, or 2 depending on whether Wordle returned grey, yellow, or green for each particular letter.
) {
  my @words = $wordlist.IO.lines.grep(/ ^ <[a .. z]>**5 $ /);
  say "Loaded wordlist from $wordlist, found {@words.elems} five letter words containing only 'a' through 'z'.";
  for @guess -> $guess {
    $guess ~~ / ^ (\w+) \, (<[012]>+) $ /;
    say "Applying guess {$0.uc} to {@words.elems} words with feedback {$1}";
    my $pre_guess_count = @words.elems;
    for $0.lc.comb Z $1.comb Z (0..*) -> ($char, $feedback, $idx) {
      given $feedback {
        when 0 { 
          given @words.grep({ !.contains($char) }) {
            say "  Rejecting {@words.elems - $_.elems} words containing {$char.uc}";
            @words = $_
          }
        }
        when 1 {
          given @words.grep({ .contains($char) }) {
            say "  Rejecting {@words.elems - $_.elems} words not containing {$char.uc}";
            @words = $_;
          }

          given @words.grep({ .comb[$idx] !eq $char }) {
            say "  Rejecting {@words.elems - $_.elems} words containing {$char.uc} at position $idx";
            @words = $_;
          }
        }
        when 2 {
          given @words.grep({ .comb[$idx] eq $char }) {
            say "  Rejecting {@words.elems - $_.elems} words not containing {$char.uc} at position $idx";
            @words = $_;
          }
        }
      }
    }
    my $post_guess_count = @words.elems;
    my $guess_bits = log2($pre_guess_count) - log2($post_guess_count);
    say " This guess eliminated {$pre_guess_count - $post_guess_count} words and contained $guess_bits bits of information."
  }
  say "There are {@words.elems} words left.";
  @words.say
}
```

# A game of bits

As mentioned in the 3b1b video, each guess in a missing information game like this will correspond to a certain number of bits. A "one bit" guess is a guess that splits your search space in half, a "two bit" guess is a guess that splits your search space in quarters, and so on.

When my program starts, it loads your system wordlist and filters out any words that Wordle will definitely not use (namely: proper nouns, contractions, and possessives). 

```
sub MAIN(
  Str :wl(:$wordlist) where *.IO.f = '/usr/share/dict/words', #= The wordlist
  *@guess where .all ~~ Guess #= Wordle guesses, given as space-delimited command line arguments. Each guess is of the form <word>,<feedback> where <word> is the guessed word and <feedback> is 0, 1, or 2 depending on whether Wordle returned grey, yellow, or green for each particular letter.
) {
  my @words = $wordlist.IO.lines.grep(/ ^ <[a .. z]>**5 $ /);
  say "Loaded wordlist from $wordlist, found {@words.elems} five letter words containing only 'a' through 'z'.";
```

At least on my system, this means that we start with 4,562 words to choose from. This is our search space. Therefore, a one bit Wordle guess will narrow down our space to 2,281 valid guesses remaining, a two bit guess narrows it down to 1,140 valid guesses remaining, et cetera. 

Each Wordle guess will give you a different amount of information. The exact amount of information that each guess gives you can be calculated logarithmically. We may interpret a base-2 logarithm as an answer to the question "how many times may this space be split in half before we have exactly one answer left". In our case `log_2(4562) = 12.155`, so if every Wordle guess eliminated exactly half the words we might expect a wordle game to take 12 or 13 guesses. Of course, Wordle ends in 6 guesses, indicating that each individual guess must do more than split the space in half. Each guess must be more than one bit.

How many bits does each guess need to have? Through the magic of the additive identites of logarithms, we may calculate that by simply noting that there are 6 guesses and 12.155 required bits of information. So each guess needs to have at least `12.155 / 6 = 2.03` bits of info. 

The crux of the situation is that better guesses have more bits. And since we know the bits per guess required to win the game in 6 guesses, we also know when our guesses are bad enough that we're going to lose the game. If `a` is the size of our search space before we make our guess, and `b` is the size of the search space after we make our guess, then the information our guess has is exactly `log_2(a) - log_2(b)`. This calculation is run automatically by the solver and reported out.

# What does the info do?

When you make a guess, the game returns a little bit of information for each letter in your guess. It's either highlighted grey, indicating a letter which does not appear in the answer; yellow, indicating a letter which appears in different position in the answer; or green, indicating a letter which appears in that exact position in the answer. Each individual color may be used to filter the wordlist in a different way. This was the fun part: trying to figure out how to represent the intuition that we have about these different bits of info. We may go one by one, looking at each case individually.

## My letter's grey!

In the case that a letter in your Wordle guess is grey, you know that letter won't appear at all in the final answer. So there's no point in continuing to guess words that contain that letter. The game does a good job of representing this by disabling the grey letters in the on screen keyboard at the bottom.  

We may represent this by removing any word containing this particular letter from our possible guesses.

```
given @words.grep({ !.contains($char) }) {
  say "  Rejecting {@words.elems - $_.elems} words containing {$char.uc}";
  @words = $_
}
```

## My letter's yellow!

In the case that a letter in your guess is yellow, you actually get two separate pieces of information. Intuitively, it feels like "this letter is in the final guess somewhere" should be a single cohesive piece of info. However, consider the case of the words "BREAK" and "BRAKE". Same letters, different order. If the answer was supposed to be "BREAK" and you guessed "BRAKE", Wordle would make the B green, the R green, the A yellow, the K yellow, and the E yellow. If we consider just "this letter is in the final guess somewhere", then we're free to scramble the last three letters of the word however we want. Perhaps the answer is really "BREKA". Or maybe it's "BRAEK". 

In reality, we may filter these possible guesses out (and not just because they're not real words)! When Wordle told us that the A in "BRAKE" was yellow, it ruled out the possibility of "BRAEK" because we know the A must not be in the third position -- or else it would be green! Likewise, telling us that the K was yellow ruled out the word "breka". 

So we must filter the wordlist twice if we see a yellow letter. The first time removing any words that don't contain the yellow letter,

```
given @words.grep({ .contains($char) }) {
  say "  Rejecting {@words.elems - $_.elems} words not containing {$char.uc}";
  @words = $_;
}
```

and the second time removing any words that do contain the yellow letter at the exact position it appeared in the guess.


```
given @words.grep({ .comb[$idx] !eq $char }) {
  say "  Rejecting {@words.elems - $_.elems} words containing {$char.uc} at position $idx";
  @words = $_;
}

```

## My letter's green!

In the case that a letter in your guess is green, I think that the intuition for how to play aligns pretty well with what the code must do. We remove every word except for the ones that contain the green letter in the correct position.

```
given @words.grep({ .comb[$idx] eq $char }) {
  say "  Rejecting {@words.elems - $_.elems} words not containing {$char.uc} at position $idx";
  @words = $_;
}
```

# Future considerations

## Repeated letters

Wordle does something kind of strange to handle repeated letters. If the secret answer is "STEAK", and your first guess was "BLESS", then Wordle would highlight the first S yellow and the second S grey. This has always struck me as weird. It technically gives you more information -- it tells you that there definitely are not two S's in the answer. But as far as I'm aware that's not how Wordle's predecessor, Mastermind, works. And from what I've seen it's not how any of the Wordle clones work either. If you input that guess into my program, well,
 
```
./main.raku bless,00210
Loaded wordlist from /usr/share/dict/words, found 4562 five letter words containing only 'a' through 'z'.
Applying guess BLESS to 4562 words with feedback 00210
  Rejecting 523 words containing B
  Rejecting 1032 words containing L
  Rejecting 2782 words not containing E at position 2
  Rejecting 90 words not containing S
  Rejecting 16 words containing S at position 3
  Rejecting 119 words containing S
 This guess eliminated 4562 words and contained Inf bits of information.
There are 0 words left.
[]
```

It doesn't work, because it first eliminates all words that don't contain S, and immediately follows that up by eliminiating all the words that do contain S. 

My solution? ~~Just don't guess words with double letters. They're gonna give you less information than guessing without double letters, unless you get lucky enough that the answer has that exact double letter as well. ~~

In reality, I'd like to make my program support double letters, but I'm at a bit of a loss for an elegant way to do it. It seems to be that the only special case needed is when the first double letter is yellow or green and the second one is grey. For example, imagine guessing "BLESS" when the hidden answer is "SERFS". Now, the first S is yellow and the second S is green.

```
./main.raku bless,00112
Loaded wordlist from /usr/share/dict/words, found 4562 five letter words containing only 'a' through 'z'.
Applying guess BLESS to 4562 words with feedback 00112
  Rejecting 523 words containing B
  Rejecting 1032 words containing L
  Rejecting 1527 words not containing E
  Rejecting 225 words containing E at position 2
  Rejecting 701 words not containing S
  Rejecting 54 words containing S at position 3
  Rejecting 153 words not containing S at position 4
 This guess eliminated 4215 words and contained 3.7166588787338775 bits of information.
There are 347 words left.
[aches acmes acres adzes aegis aeons aides antes apses arses ashes asses cages cakes canes capes cares cases caves cedes cents cites codes cokes comes cones copes cores cotes coves cries cures dames dares dates dazes deans dears decks demos dents desks dices dikes dimes dines dives domes dopes doses dotes doves dozes dries dudes dukes dunes dupes dykes earns eases eaves echos edges edits emirs emits epics ethos euros exams exits expos faces fades fakes fares fates faxes fazes fears feats fends ferns fests fetus feuds fezes fifes fines fires fives fixes fores foxes fries fumes fuses games gapes ...]
```

Let's add one more guess to narrow it down more and see if "SERFS" is in the answer space.

```
./main.raku bless,00112 fuses,10112
Loaded wordlist from /usr/share/dict/words, found 4562 five letter words containing only 'a' through 'z'.
Applying guess BLESS to 4562 words with feedback 00112
  Rejecting 523 words containing B
  Rejecting 1032 words containing L
  Rejecting 1527 words not containing E
  Rejecting 225 words containing E at position 2
  Rejecting 701 words not containing S
  Rejecting 54 words containing S at position 3
  Rejecting 153 words not containing S at position 4
 This guess eliminated 4215 words and contained 3.7166588787338775 bits of information.
Applying guess FUSES to 347 words with feedback 10112
  Rejecting 318 words not containing F
  Rejecting 25 words containing F at position 0
  Rejecting 0 words containing U
  Rejecting 0 words not containing S
  Rejecting 0 words containing S at position 2
  Rejecting 0 words not containing E
  Rejecting 1 words containing E at position 3
  Rejecting 0 words not containing S at position 4
 This guess eliminated 344 words and contained 6.8538293518571045 bits of information.
There are 3 words left.
[hefts serfs wefts]
```

Looks like it works!

