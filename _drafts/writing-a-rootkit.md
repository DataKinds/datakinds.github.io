---
layout: post
title: Writing a rootkit, by someone who knows nothing about computer security
date: 2018-06-21 15:36 -0700
---
For some God awful reason, my most popular repo on Github is [syscall-rootkit](https://github.com/Aearnus/syscall-rootkit). It's a tiny Linux kernel module that intercepts syscalls to `read(2)` and `write(2)`. As of writing this blog post, it has 11 stars and 8 forks. Fingers crossed that those 8 people are using the code for good.

As far as how the program works, it's actually very simple. Once compiled, it can be hot loaded into the kernel as a kernel module (using `insmod`) and it replaces the pointers of `read(2)` and `write(2)` with pointers to its own `rk_read` and `rk_write` functions. [The code is simple enough to fit on one page](https://github.com/Aearnus/syscall-rootkit/blob/master/rk.erb.c).

Compiling the code, on the other hand, is a different issue. One that I'm in _way_ over my head trying to solve.

KASLR
---
(or, why this isn't actually a rootkit).

Typically, from the information I've gathered browsing Wikipedia and watching Youtube, a rootkit has a few very important properties:

1. It buries itself deep into the innards of a system.

2. It persists across boots and removal attempts.

3. It makes a reasonable attempt to hide itself from the end user.

4. It installs itself surreptitiously.

Now, uhm, let's look at which of these syscall-rootkit manages:

1. :heavy_check_mark: It buries itself deep into the innards of a system.

2. :x: It persists across boots and removal attempts.

3. :x: It makes a reasonable attempt to hide itself from the end user.

4. :x: It installs itself surreptitiously.

So, calling this project a rootkit in the first place is optimistic at best.

Points 3 and 4 are really only of concern for people who want to do harm, and I'm not really here to hurt anybody. So, throw those out the window and only focus on points 1 and 2. We've got point 1 down pat: I'm not sure any program could get lower and deeper than the kernel [without](https://hackaday.com/2017/12/11/what-you-need-to-know-about-the-intel-management-engine/) [getting](https://en.wikipedia.org/wiki/Intel_Management_Engine#Security_vulnerabilities) [into](https://thehackernews.com/2018/02/airgap-computer-hacking.html) [any](https://arstechnica.com/information-technology/2013/10/meet-badbios-the-mysterious-mac-and-pc-malware-that-jumps-airgaps/) [funny business](https://www.theregister.co.uk/2015/08/11/memory_hole_roots_intel_processors/).

Last, but not least, we come to point 2. As a random guy and not a security researcher, the concept of a program trying to evade and block removal attempts is...

![How I feel](/assets/imgs/writing-a-rootkit/whoosh.png)

That leaves the final point to talk about. The first part of point 2 points out the fact that the rootkit must "persist across boots". This is an issue because of KASLR. **K**ernel **A**dress **S**pace **L**ayout **R**andomization a security measure implemented in the Linux kernel which ensures that every important bit of data and every table in the kernel's memory is placed at a random spot at every boot-up. This is to prevent idiots like me from overwriting the syscall table. KASLR is generally not a very large impediment to a black hat who's sufficiently dedicated to causing harm, with the Google search for "bypass Linux KASLR" returning 18,300 results

![bypass Linux KASLR](/assets/imgs/writing-a-rootkit/kaslr_linux.png)

and a Google search for "bypass Windows KASLR" revealing a writeup on a [longstanding Windows NT bug to bypass KASLR up until Windows 8.1](https://www.crowdstrike.com/blog/kaslr-bypass-mitigations-windows-81/).

Because of the fact that the point of my project wasn't to exploit KASLR (and out of laziness), I took a little bit of an easier approach to all of this. This approach, though, doesn't allow the "rootkit" to persist across boot-ups. Enter [make.rb](https://github.com/Aearnus/syscall-rootkit/blob/master/make.rb).

This hack of a script uses a templating engine to bake the address of the KASLR-hardened syscall table right into the code itself. To find this address, the script accesses a read-only file system exposed by the Linux kernel at `/boot/`. In `/boot/`, there exist `System.map` files which contain the absolute offsets of every single symbol in the kernel address space for that session (or possibly for that version of the kernel? I'm not too sure, actually).

![System.map](/assets/imgs/writing-a-rootkit/system_map.png)

_A screencap of the a bit of my System.map file, near the top._

It just so happens that the table that stores all the Linux system calls is also listed in the `System.map` file.

```ruby
linuxVersion = `uname -r`.chomp
puts "uname -r = #{linuxVersion}"
puts "using /boot/System.map-#{linuxVersion}"
syscallOffset = `sudo cat /boot/System.map-#{linuxVersion} | grep ' sys_call_table' | ruby -ane 'print $_.split(" ")[0]'```

This little block of Ruby code serves two purposes. The first purpose is to prove to me that anything can work with enough hacking and duct tape. The second purpose is to store the offset for the syscall table (labelled `sys_call_table`) in a variable `syscallOffset`.
