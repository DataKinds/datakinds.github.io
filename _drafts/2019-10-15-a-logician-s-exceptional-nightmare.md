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

The kettle is steaming and whistling now, and you know you have little time left before you must depart. 