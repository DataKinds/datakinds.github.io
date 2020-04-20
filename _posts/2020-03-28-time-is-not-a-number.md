---
layout: post
title: Time is not a number
date: 2020-03-28 16:03 -0700
---

What is a number? Numbers should have a couple distinct features as, say, members of a ring: an addition and multiplication operator, associativity, commutativity, and the distributive property.

Even more fundamentally, adding two numbers together should probably give you back another of the same kind of number.

This brings me to a couple days ago, trying to set up a time tracking spreadsheet in Google Sheets. Sheets lets you press `C-:` to insert the current time, so I figured it'd be easy to set up a little spreadsheet where I could clock in and clock out and have Sheets automatically calculate rates and generate sample invoices and all that fun stuff.

![Time in and time out.](/assets/imgs/time-is-not-a-number/1.png "Time in and time out.")

When I went to subtract time in from time out, I was met with some odd behavior.

![I've been working for 12:12:25 AM hours.](/assets/imgs/time-is-not-a-number/2.png "I've been working for 12:12:25 AM hours.")

It makes sense, I guess? Sheets expects that when you add and subtract two things together that you'll get a third thing of the same type. But time doesn't work like that -- subtracting one time from another time doesn't get you time, it gets you a duration.

Even more than that, I can't for the life of me interpret what adding two times together would mean. I'm tempted to say that "adding time" is a meaningless concept! Rails doesn't let you do it; instead, you're only allowed to add a duration to a time

So time doesn't have a well defined addition operator, and subtraction on time produces something that's not time-like at all.

Eventually, I at least figured out that Google Sheets is capable of producing durations, it's just a matter of searching the context menus until you can find the duration numeric type.

![The hidden duration option.](/assets/imgs/time-is-not-a-number/3.png "The hidden duration option.")

So, I continued on my merry way until I added a column which tries to automatically calculate the amount owed for a certain duration of work.

![50 cents?](/assets/imgs/time-is-not-a-number/4.png "50 cents?")

"There is no way that a half hour of work only nets me 55 cents," I thought to myself. I troubleshooted this for a few hours. "Surely," I assured myself, "somebody else on the internet has done this before." But I could only find one blog post, and their solution was to write a parsing function to parse out the hours, minutes, and seconds in the time cell. That was way too fragile for me to want to deal with, so I set out to figure out how Google Sheets handles duration as a number.

And after searching long and hard? I still have absolutely no clue.

![What is this number I don't even](/assets/imgs/time-is-not-a-number/4.png "What is this number I don't even")

Turns out that I get the right result by multiplying the duration-converted-to-a-number by a mystery scalar `24`.

![Magic numbers.](/assets/imgs/time-is-not-a-number/6.png "Magic numbers.")

Would be fantastic if I could know what brought this number out from the depths of Google Sheets. Converting between time-esq types seems like a fantastic argument in favor of type systems to me.
