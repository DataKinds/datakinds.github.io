---
layout: post
title: "Raku in June 2020: Speed, Usability, and Politics"
date: 2020-06-17 16:50 -0700
---

# Preface

**Hey all. The date is June 17th, 2020. I don't want to wax poetic about current events in this post, so I will keep this incredibly brief: if you don't support the Black Lives Matter movement, the Black Trans Lives Matter movement, or if you think everything that's been happening doesn't really apply to you and that you don't need to do anything: please close this window and re-evaluate your priorities. This is not "unnecessarily political": everything we do as developers and engineers is inherently political. We control the modern digital world, and doing so without regard for the minority groups we may affect is dangerous and utterly reprehensible. A great link that's been going around with ways you can get involved is here: [https://blacklivesmatters.carrd.co/](https://blacklivesmatters.carrd.co/). If you'd like to educate yourself on the issues that are currently at hand, a book I've had highly recommended to me is [The New Jim Crow by Michelle Alexander](https://www.goodreads.com/book/show/6792458-the-new-jim-crow). Another piece that's more relevant than ever is the late Martin Luther King Jr.'s [Letter from a Birmingham Jail](https://www.africa.upenn.edu/Articles_Gen/Letter_Birmingham.html).** 

It's been a few months since I looked at Raku. Last time I fiddled with it was when the language finally took the leap and renamed itself from Perl 6 to Raku. Heck, I'm still in the old Perl 6 GitHub organization, and I parted ways before the new Raku organization was established.

A few months later and I've gotten the itch to play with it once again. Something about the corporate environment I work in on the daily really drove me away from wanting to use other, more mainstream technologies on my spare time. What's the fun in sitting down to work on a weekend passion project if you're just doing the exact same thing you do at your job?

At work, we use Ruby on Rails -- so I've become acquainted with Ruby on a deeper level than I ever had before. Because of that, I'd like to use Ruby as a bit of a baseline for my judgments on Raku. I think it's an apt comparison because Ruby has many of the same goals as Raku does: it optimizes for developer happiness, it's not fast but it's fast enough for what it's used for, and it's incredibly expressive in ways that other languages couldn't be.

# Installation

It could not be easier to install either of these languages and to have a working environment up in minutes. I have zero complaints -- hell, I have less than 0 complaints. The tooling here is phenomenal: major props to [Michal Papis and the other folks working on RVM](https://github.com/orgs/rvm/people), and major props to [Patrick Böker, Tadeusz Sośnierz, and the other folks working on rakubrew](https://github.com/Raku/App-Rakubrew/graphs/contributors).

Once RVM and rakubrew are installed, getting both environments ready for testing is this easy:

```bash
$ rvm install ruby-head
### ... wait for a few minutes... ###
$ rakubrew build moar-blead master
### ... wait for a few minutes... ###
```

I'll be testing the current head versions of both Ruby and Raku. At the time of this writing, these are:

* `ruby 2.8.0dev (2020-06-17T16:16:21Z master 41a4c80d28) [x86_64-linux]`
* `This is Rakudo version 2020.05.1-292-gcd6172480 built on MoarVM version 2020.05-97-gb5bb4f8d1 implementing Raku 6.d.`

Granted, I realize that Ruby 2.7 and 2.8 have made waves for being notoriously regressive. The JIT has to touch the disk to function (???), libraries generally only support up to 2.6.5, so on and so forth. In the spirit of fair competition I'm going to proceed with using the bleeding edge of both of these languages. 

# Performance

## Cold start

I'm expecting Ruby to blow Raku out of the water with cold start performance -- MoarVM has an extremely significant warm up period. In my past experience, larger Raku projects can take up to almost 10 seconds to warm up and compile on first run, and while compiling, the system was more or less locked up. I'm eager to see if this is still the case. 

To test this, I'm just going to invoke `ruby` and `raku` on an empty file some obscene number of times and time it.

```bash
$ time (for i in {1..200}; do ruby empty_file; done)

real    0m14.165s
user    0m12.273s
sys     0m2.074s

$ time (for i in {1..200}; do raku empty_file; done)

real    0m24.450s
user    0m25.020s
sys     0m4.352s
```

Just as I predicted, Ruby outperformed Raku in cold start performance. There was not as much of a difference as I expected, though: Ruby's cold start performance was only 1.73 times that of Raku's.

Just for consistency's sake, let's try it again with a higher iteration count.

```bash
$ time (for i in {1..1000}; do ruby empty_file; done)

real    1m10.746s
user    1m2.334s
sys     0m9.077s

$ time (for i in {1..1000}; do raku empty_file; done)

real    2m1.846s
user    2m4.770s
sys     0m21.252s
```

Again, we're seeing that Ruby seems to start exactly 1.7 times faster than Raku. From my testing, that seems consistent.

Raku starting up cold in 0.12 seconds is comparable to [the base JVM's startup time](https://purelyfunctional.tv/article/the-legend-of-long-jvm-startup-times/) and much less than plenty of other JIT compiled languages. Color me impressed.

## Garbage collection

In the real world, garbage collection is a _complicated_ beast. There's no one solution that fits every situation, and a GC that may speed through one benchmark might be an absolute dealbreaker for another benchmark. There are too many moving parts here to count: how many objects are being allocated? how big are the objects? how do they align with memory pages? how fast are you allocating them? how often are they being accessed?... well, you get the idea.

To get a little image of the performance of Raku's GC in comparison to Ruby's GC, I'd like to look at both the time taken by a simple program and its memory usage.

The programs I'm comparing will simply create X objects and then promptly end. I'm writing them to best practices in each language, regardless of any micro-optimizations I may be able to make with how the loop is structured or how the objects are created.

The Ruby program:

```rb
class Simple
    def initialize()
        @hello = "Hello, world!"
    end
end

1_000_000.times do |i|
    simple = Simple.new()
end
```

The Raku program:

```p6
class Simple {
    has $.hello;
    submethod BUILD() {
        $!hello = "Hello, world!";
    }
}

for ^1_000_000 -> $i {
    my $simple = Simple.new()
}
```

Valgrind's `massif` tool can make quick work of these scripts: `valgrind --tool=massif ruby empty_file`