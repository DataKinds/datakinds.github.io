---
layout: post
title: 'The Beauty Of Brevity: Tiny Code By Example'
date: 2019-05-29 16:35 -0700
---
Today, I wrote a little program to download and view entries off the SCP website.

For the uninitiated, the SCP website is comprised of a community of horror writers who write stories centered around a cohesive fictional universe where the "SCP Foundation" exists to protect the public from anomalous and reality-bending objects. 

Reading all of these stories is an absolute blast (it counts as cardio!), but their website is unfortunately rather cluttered. [Here's a link to a random SCP if you want to check it out yourself](http://www.scp-wiki.net/random:random-scp). I wanted an easier way to browse -- so I wrote a Perl 6 script.

![Before/after image of SCP-173](/assets/imgs/the-beauty-of-brevity/before-after.png)

<div style="width: 100%; text-align: center;"><i>Before/after image of SCP-173</i></div>

Without further ado, here's the entirety of the source code for the Perl 6 SCP viewer:

{% highlight perl %}
#!/usr/bin/env perl6

use WWW;
use DOM::Tiny;

sub cute-line($scp-number) {
    split("-\n", qq:to/END/).pick.chomp;
    Accessing...-
    O5 authorization required...
    Authorized!-
    Dispatching search for SCP-$scp-number...-
    Database username: {qx{whoami}.chomp}
    Database password: ****************
    Database query: SCP-$scp-number
    END
}

sub MAIN($scp-number) { 
    CATCH {
        default { .payload.say }
    }

    say cute-line($scp-number);
    my $page = get "http://www.scp-wiki.net/scp-$scp-number" or die "Couldn't find the database entry for SCP-$scp-number.";
    my $dom = DOM::Tiny.parse: $page;

    my $tmp-filename = '/tmp/' ~ (roll 16, 'a'..'z').join ~ '.html';
    my $tmp-file = open $tmp-filename, :w;
    $tmp-file.print: "$dom.find('style')";
    $tmp-file.print: "$dom.find('script')";
    $tmp-file.print: q:to/END/;
        <style type="text/css">
            #main-content {
                margin: 0 auto !important;
            }
        </style>
    END
    $tmp-file.print: "$dom.at('#main-content')";
    $tmp-file.close;

    qqx{xdg-open '$tmp-filename'}; 
}
{% endhighlight %}

It's short _enough_, and it works, but I would not call this code elegant in the slightest. It is overly verbose, it is rather cluttered, and it uses unnecessary variables. It's readable, but it's not something that I'd enjoy reading -- not to get all artsy-fartsy; but this code has no plot, no symbolism, nothing that makes the reader say "Huh, I like that!"

"Why," you may ask, "does it matter if the programmer doesn't like reading their code? After all, code is meant to be read by a computer, not by a person. And hey, it works..." Your voice trails off into the abyss.

Blasphemy! If you do not enjoy reading your code, then writing it must have been misery. Code reviews will be hellish torment. 

Code that just works is the bare minimum. The code above is a shoddy rope swing over a gap that could be united by an ornate drawbridge. If you're a techno-Indiana Jones, then maybe rope swings are more your style. More power to you. But I'd like to explore how we could turn this code into that jewel-encrusted plot-laden mega-walkway I want it to be.

# The Elegantification

Without fiddling with too much of the program itself, we can immediately make this code more elegant by condensing sequential statements into method chains and removing redundant variables.

```perl
#!/usr/bin/env perl6

use WWW;
use DOM::Tiny;

sub cute-line($scp-number) {
    split("-\n", qq:to/END/).pick.chomp;
    Accessing...-
    O5 authorization required...
    Authorized!-
    Dispatching search for SCP-$scp-number...-
    Database username: {qx{whoami}.chomp}
    Database password: ****************
    Database query: SCP-$scp-number
    END
}

sub MAIN($scp-number) { 
    CATCH {
        default { .payload.say }
    }

    say cute-line($scp-number);
    my $dom = DOM::Tiny.parse: (get "http://www.scp-wiki.net/scp-$scp-number" or die "Couldn't find the database entry for SCP-$scp-number.");

    my $tmp-filename = '/tmp/' ~ (roll 16, 'a'..'z').join ~ '.html';
    (open $tmp-filename, :w).&{ 
        .print: "$dom.find('style')";  
        .print: "$dom.find('script')"; 
        .print: q:to/END/;
            <style type="text/css"> #main-content { margin: 0 auto !important; } </style>
            END
        .print: "$dom.at('#main-content')";
        .close; };
    qqx{xdg-open '$tmp-filename'}; 
}
```

We have no use for keeping flavor text in its own function.

```perl
#!/usr/bin/env perl6

use WWW;
use DOM::Tiny;

sub MAIN($scp-number) { 
    CATCH {
        default { .payload.say }
    }

    say split("-\n", qq:to/END/).pick.chomp;
        Accessing...-
        O5 authorization required...
        Authorized!-
        Dispatching search for SCP-$scp-number...-
        Database username: {qx{whoami}.chomp}
        Database password: ****************
        Database query: SCP-$scp-number
        END
    my $dom = DOM::Tiny.parse: (get "http://www.scp-wiki.net/scp-$scp-number" or die "Couldn't find the database entry for SCP-$scp-number.");

    my $tmp-filename = '/tmp/' ~ (roll 16, 'a'..'z').join ~ '.html';
    (open $tmp-filename, :w).&{ 
        .print: "$dom.find('style')";  
        .print: "$dom.find('script')"; 
        .print: q:to/END/;
            <style type="text/css"> #main-content { margin: 0 auto !important; } </style>
            END
        .print: "$dom.at('#main-content')";
        .close; };
    qqx{xdg-open '$tmp-filename'}; 
}
```

And we should keep formatting consistent.

```perl
#!/usr/bin/env perl6

use WWW;
use DOM::Tiny;

sub MAIN($scp-number) { 
    CATCH { default { .payload.say } }
    say split("-\n", qq:to/END/).pick.chomp;
        Accessing...-
        O5 authorization required...
        Authorized!-
        Dispatching search for SCP-$scp-number...-
        Database username: {qx{whoami}.chomp}
        Database password: ****************
        Database query: SCP-$scp-number
        END
    my $dom = DOM::Tiny.parse: (get "http://www.scp-wiki.net/scp-$scp-number" or die "Couldn't find the database entry for SCP-$scp-number.");
    my $tmp-filename = "/tmp/{ (roll 16, 'a'..'z').join }.html";
    (open $tmp-filename, :w).&{ 
        .print: "$dom.find('style')";  
        .print: "$dom.find('script')"; 
        .print: q:to/END/;
            <style type="text/css"> #main-content { margin: 0 auto !important; } </style>
            END
        .print: "$dom.at('#main-content')";
        .close; };
    qqx{xdg-open '$tmp-filename'}; }
```

We could even get rid of the `$tmp-filename` variable.

```perl
#!/usr/bin/env perl6

use WWW;
use DOM::Tiny;

sub MAIN($scp-number) { 
    CATCH { default { .payload.say } }
    say split("-\n", qq:to/END/).pick.chomp;
        Accessing...-
        O5 authorization required...
        Authorized!-
        Dispatching search for SCP-$scp-number...-
        Database username: {qx{whoami}.chomp}
        Database password: ****************
        Database query: SCP-$scp-number
        END
    my $dom = DOM::Tiny.parse: (get "http://www.scp-wiki.net/scp-$scp-number" or die "Couldn't find the database entry for SCP-$scp-number.");
    (open "/tmp/{ (roll 16, 'a'..'z').join }.html", :w).&{ 
        .print: "$dom.find('style')";  
        .print: "$dom.find('script')"; 
        .print: q:to/END/;
            <style type="text/css"> #main-content { margin: 0 auto !important; } </style>
            END
        .print: "$dom.at('#main-content')";
        .close; 
        qqx<xdg-open '{ .path }'>; }; }
```

# The Conclusion

> An apocryphal story. At the first of the three universities he claims to have been thrown out of, [Arthur] Whitney’s class was given an assignment: write a program tha will print the most successive prime numbers possible with limited CPU time and limited green-striped paper. (Yes, that long ago.) His solution won by a handsome margin and was disqualified on two counts. In the first place he had ignored everything the class had been taught about modularisation and code re-use. He just wrote code optimised to solve one problem spectacularly fast. He had also noticed the problem did not specify printing spaces between the primes. The printouts were a sea of ink. And his code looked like woodgrain. 

_~Stephen Taylor, from "Impending kOS" ([http://archive.vector.org.uk/art10501320](http://archive.vector.org.uk/art10501320))_

I love the idea of code being like woodgrain, so I'm going to run with it. 

You have your Ikea grade particle board -- one cohesive piece that looks okay at best. It arguably gets the job done. You can put your books on it, and it'll glue your microservices together. But it's ugly and it's no fun to put together. Have you ever tried to maintain a piece of particle board? It crumbles under your fingers, just as ugly looking software does when you try to maintain it. Nobody really enjoys working with particle board, just the same way that nobody really enjoys working with that ugly, inelegant VBA6 module that holds their business together.

But, gleaming in the distance, is the most beautiful redwood plank you've ever seen. It's deep and pristine, it's easy on the eyes, and its grain feels smooth under your fingers. This is the kind of software we should all strive to write. Code whose details interweave and visibly intermingle. Code that a developer can cut up any way they want to, and still use it to make a viable product.

---

Maybe I got a little bit carried away with that metaphor, but the gist is that elegant code is

* smaller
* easier to maintain
* fun to read

with emphasis on point 3. Programming languages are languages after all. Why not let them be beautiful and deep just as human language is?