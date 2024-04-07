---
title: "A IP Calculator For Me"
date: 2024-04-06T20:09:27+08:00
tags:
categories:
draft: false

---

I have written a IP calcultor for personal use. Yes, there is a command line
version the great, web based [ipcalc](https://jodies.de/ipcalc). I only
recently discovered that and been using it since. That probably only delayed
writing my own.

Why would you want to reinvent ipcalc, you may ask. Because there are few
little features I need.

First, when I get a big CIDR and have to split it up into subnets, perhaps of
uneven size, I want to to know the start of the next possible subnet.

I also want to know if a given IP is within a given CIDR range. And in the same
spirit, I want check for posisble CIDR overlaps.

I had implemented all these capabilities, previously in a different (private)
tool (In Go). Hence I knew it only takes a little effort. Also, with net/netip,
overlaps checks are built in!

And here goes [ipeter](https://codeberg.org/chanux/ipeter)

Naming is hard. I just named it after a colleague who I go to when I want some
IPs I can safely use in my $dayjob.

Let me show you it's features

```
> ipeter calc 10.1.0.0/28
Network:	10.1.0.0/28 (Class A)
Netmask:	255.255.255.240
Broadcast:	10.1.0.15
Hosts/Net:	14
Next:		10.1.0.16
```

As you can see, calc is inspired by ipcalc. Plus it shows the next IP.

```
> ipeter cidr 10.0.1.0/24 overlaps 10.0.0.0/24
false
> ipeter cidr 10.0.1.0/24 overlaps 10.0.0.0/8
true
```

I went a little experimental with the interface for this, using the positional aruments
branching featue in [alecthomas/kong](https://github.com/alecthomas/kong?tab=readme-ov-file#branching-positional-arguments).

Kong has been my favorite go cli library for a while now. The ease of use and
the features feel just right for me!

Next up, I also did the check for ip being within a CIDR in the same style as
above.

```
> ipeter ip 10.0.0.36 in 10.0.0.0/27
false
> ipeter ip 10.0.0.1 in 10.0.0.0/27
true
```

However, to check multiple IPs at once, there's also 

```
> ipeter contains 100.64.0.0/10 192.168.10.1 10.1.0.0 100.100.1.1
192.168.10.1 : false
10.1.0.0	 : false
100.100.1.1	 : true
```

And las but not least, quickly find out the netmasks that can house a given
number of hosts

```
> ipeter mask 345 34
netmask for 345 hosts is /23
netmask for 34 hosts is /26
```

Turns out I can do whatever I want when the intended audience is just one and
it's just me! Don't use this software. Or do use. Do whatever you want.


**Asides:**

A IP calulator? [Yes](https://www.youtube.com/watch?v=mJUtMEJdvqM)!

"Let me show you it's features" is [a phrase commonly
used](https://www.youtube.com/watch?v=qTYY2m3rtYU) by Joerg Sprave at the
slingshot channel.
