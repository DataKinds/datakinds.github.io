---
layout: post
title: A Logician's Exceptional Nightmare
date: 2019-10-15 12:27 -0700
---

You wake up in a cold sweat, beads of salty adrenaline pouring down your forehead. The entire night, you were tossing and turning and dreaming an awful nightmare. You let your mind drift towards the dark underbelly of your life's expertise. But you, a logician, are determined to get to the bottom of this. So you grab your dream journal and start scribbling.

"It started in a world," you begin to remember, "with the strangest rules for truth and falsehood." As if propelled by a force outside your own body, you pen down the following arcane markings:

```perl
unit module CursedLogic;

sub f is export { Nil }
sub t is export { fail }
```

You recant a mantra you heard whispered to you in the dreamscape: "Truth is void and false bubbles up." Strange.

Suddenly, a tremor shoots up your right arm. Your head droops and you vaguely remember the outlines and weak edges of relationships that accompanied these singleton values.

First to arrive in your minds eye is a way to turn truth into falsehood, a method through which you may transmute the fierce bubbling of falseness into a calm truthiness.

```perl
sub _not($a) is export {
  sink $a;
  CATCH {
    return f
  }
  return t
}
```

The clock tower outside your bedroom strikes 7 sharp. Seven terse, shrill beats humming through the window and into your skull. You hear the neighbors fighting in the next apartment over. The birds chirp and your lover puts a kettle on the stove the floor below you. All of it hits you simultaneoulsy -- and you, in your little intersection of the facets of the universe, pen down another relationship:

```perl
sub _and($a, $b) is export {
  sink $a;
  CATCH {
    sink $b;
    CATCH {
      return t
    }
    return f
  }
  return f
}
```

Faster and faster now, you're starting to recall all the disgusting intracacies of your hardly consistent dream world. You recalled placing both truth and falsehood abreast of one another, and taking whatever may have bubbled up away from the two. You quickly write that down in your dream journal, before the fleeting thought disappears entirely.

```perl
sub _or($a, $b) is export {
  sink $a; sink $b;
  CATCH {
    return t
  }
  return f
}
```

The kettle is steaming and whistling now, and you know you have little time left before you must depart. You gaze out the window and towards the blue sky, feeling the universe itself bear down onto you. "One more relationship... just one more!" you mutter to yourself. "One more to rule all the others..." Sometimes your dreams sound like mediocre fantasy writing.

But then it comes back to you. Blocks flowing and exceptions being caught. An eldridtch horror bubbles up from the very depths of your mind:

```perl
sub _xor($a, $b) is export {
  sink $a; 
  CATCH {
    sink $b;
    CATCH {
      return f
    }
    return t
  }
  {
    sink $b;
    CATCH {
      return t
    }
    return f
  }
}
```

Your lover calls up to you, beckoning you to start the day. 

---

<div class="language-perl highlighter-rouge" style="width:49%;display:inline-block;"><div class="highlight"><pre class="highlight"><code><span class="k">use</span> <span class="nv">Test</span><span class="p">;</span>
<span class="k">use</span> <span class="nv">lib</span> <span class="s">'.'</span><span class="p">;</span>
<span class="k">use</span> <span class="nv">CursedLogic</span><span class="p">;</span>

<span class="k">my</span> <span class="o">&amp;</span><span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="o">=</span> <span class="o">&amp;</span><span class="nv">dies</span><span class="o">-</span><span class="nv">ok</span><span class="p">;</span>
<span class="k">my</span> <span class="o">&amp;</span><span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="o">=</span> <span class="o">&amp;</span><span class="nv">lives</span><span class="o">-</span><span class="nv">ok</span><span class="p">;</span>

<span class="nv">plan</span> <span class="mi">4</span><span class="p">;</span>

<span class="nv">subtest</span> <span class="s">'or'</span><span class="p">,</span> <span class="p">{</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_or</span><span class="p">(</span><span class="nv">f</span><span class="p">,</span> <span class="nv">f</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_or</span><span class="p">(</span><span class="nv">f</span><span class="p">,</span> <span class="nv">t</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_or</span><span class="p">(</span><span class="nv">t</span><span class="p">,</span> <span class="nv">f</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_or</span><span class="p">(</span><span class="nv">t</span><span class="p">,</span> <span class="nv">t</span><span class="p">)</span> <span class="p">};</span>
<span class="p">}</span>

<span class="nv">subtest</span> <span class="s">'xor'</span><span class="p">,</span> <span class="p">{</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_xor</span><span class="p">(</span><span class="nv">f</span><span class="p">,</span> <span class="nv">f</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_xor</span><span class="p">(</span><span class="nv">f</span><span class="p">,</span> <span class="nv">t</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_xor</span><span class="p">(</span><span class="nv">t</span><span class="p">,</span> <span class="nv">f</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_xor</span><span class="p">(</span><span class="nv">t</span><span class="p">,</span> <span class="nv">t</span><span class="p">)</span> <span class="p">};</span>
<span class="p">}</span>

<span class="nv">subtest</span> <span class="s">'and'</span><span class="p">,</span> <span class="p">{</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_and</span><span class="p">(</span><span class="nv">f</span><span class="p">,</span> <span class="nv">f</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_and</span><span class="p">(</span><span class="nv">f</span><span class="p">,</span> <span class="nv">t</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_and</span><span class="p">(</span><span class="nv">t</span><span class="p">,</span> <span class="nv">f</span><span class="p">)</span> <span class="p">};</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_and</span><span class="p">(</span><span class="nv">t</span><span class="p">,</span> <span class="nv">t</span><span class="p">)</span> <span class="p">};</span>
<span class="p">}</span>

<span class="nv">subtest</span> <span class="s">'not'</span><span class="p">,</span> <span class="p">{</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">t</span> <span class="p">{</span> <span class="nv">_not</span><span class="p">(</span><span class="nv">f</span><span class="p">)</span> <span class="p">}</span>
  <span class="nv">is</span><span class="o">-</span><span class="nv">f</span> <span class="p">{</span> <span class="nv">_not</span><span class="p">(</span><span class="nv">t</span><span class="p">)</span> <span class="p">}</span>
<span class="p">}</span>

<span class="nv">done</span><span class="o">-</span><span class="nv">testing</span><span class="p">;</span>
</code></pre></div></div>

<div class="highlighter-rouge" style="width:49%;float:right;display:inline-block;"><div class="highlight"><pre class="highlight"><code>1..4
    ok 1 - 
    ok 2 - 
    ok 3 - 
    ok 4 - 
    1..4
ok 1 - or
    ok 1 - 
    ok 2 - 
    ok 3 - 
    ok 4 - 
    1..4
ok 2 - xor
    ok 1 - 
    ok 2 - 
    ok 3 - 
    ok 4 - 
    1..4
ok 3 - and
    ok 1 - 
    ok 2 - 
    1..2
ok 4 - not
</code></pre></div></div>

---

I really liked the style of Aphyr's [x'ing the technical interview](https://aphyr.com/posts/341-hexing-the-technical-interview) series, so I wanted to write my own. I was also inspired to abuse Raku's phasers by Scimon's recent talk [here](https://www.youtube.com/watch?v=dWaQn94njOo&feature=youtu.be). Feel free to leave comments using the email address below.