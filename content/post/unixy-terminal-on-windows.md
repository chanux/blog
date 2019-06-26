---
title: "Unixy Terminal on Windows"
date: 2018-03-04T09:40:00+08:00
---

When I started at my current workplace my world was shattered (Obvious
exaggeration). I realized I had to work on windows day in day out. Of course
I would be logging in to linux servers for most of the actual work but I really
missed the comfy and friendly unixy tools. Yes there were options but they did
not even come close to the user experience I were used to.

So began my search for something that would give an experience at least halfway
as good as my Linux on desktop days.. or.. okay Mac times. My search ended when
I found Babun.

Their tagline is mostly correct. "A Windows shell you will love". Once I got
this set up, the first thing I did was writing about it on an internal blog our
team did. This post is pretty much the same in spirit.

So, [Babun](http://babun.github.io/) is a collection of nice tools with good
defaults. It puts together [cygwin](https://www.cygwin.com/),
[Mintty](https://mintty.github.io/) and [ZSH](http://zsh.org) with
[Oh-my-zsh](http://ohmyz.sh/). And it works out of the box (YMMV)

Get it, install and see if you like it. One thing to note is, by default the
$HOME directory will be `C:\Users\<username>\.babun\cygwin\home\<username>`.
You can change this and [set your windows profile directory as
$HOME](http://babun.github.io/faq.html#_how_to_use_the_windows_user_profile_directory_as_my_home_directory_in_babun)

If you are here, you are likely to be enjoying corporate proxies. This is
problematic to tools like wget and curl. And of course Babun itself because it
needs to update itself. So you'd have to find the proxy url from your browser
settings and update your ~/.babunrc file. Set *http_proxy* and *https_proxy*
environemt variables accordingly (in ~/.zshrc). Or else you can set proxy
settings in ~/.wgetrc or ~/.curlrc too.

Babun offers pact a package manager which is based on cyg-apt. To try it out..

```
$ pact install tree
```

We all need tree in our lives.

BTW, not all is sunshine, trees and unicorns. pact install seems to break
things as of writing.  the remedy would be to simply run `babun update`, which
you'd need to do occassionally to keep it all updated anyway.  Guess what,
babun update is also broken and doesn't seem to get much love for now. But not
hopes are lost.

Run following in a cmd.exe

`C:\Users\<username>\.babun\update.bat`

You might want to get a coffee for the wait.

Once you know your way around this thing, it's time you personalize your
favorite editor, put together a nice SSH config etc.

If you are like me, after some time, you'd notice you miss something. And that
thing is multiple sessions. I mean tabs. This is where, excellent
[ConEmu](https://conemu.github.io/) comes in. 

Now I'll quickly go through the process of integrating Babun with it and making things a bit nicer.

Launch ConEmu and right click on tab bar to access settings. Get to **Settings > Startup > Tasks**.

[![ConeEmu settings dialogue](/blog/images/babun/conemu-babun.jpeg)](/blog/images/babun/conemu-babun.jpeg)

This is where we tweak it to load Babun. Create a new profile with [+] button,
name it Bbaun, tick "Default task for new console" and set *Task parameters* as
follows.

`/icon "%userprofile%\.babun\cygwin\bin\mintty.exe" /dir "%userprofile%\.babun\cygwin\home\<username>"`

Also set following as *Start console*

`%userprofile%\.babun\cygwin\bin\mintty.exe /bin/env CHERE_INVOKING=1 /bin/zsh.exe`

Now click on Startup and select {Babun} in *Specified named task*. This will
make Babun load in startup console. Get to **main > Confirm** and untick
"Confirm creating new console". This will stop the annoying dialogue box
everytime you open a new tab. Now hit "Save settings" button.

ConEmu and Mintty have had issue working together and ConEmu author
[says](https://github.com/Maximus5/ConEmu/issues/1382#issuecomment-357245490)
running cygwin Bash directly in ConEmu is the way to go. My colleague (Hi Daas!) tested
that but the experience was not that great. So he switched to a combination of
[Git for Windows](https://gitforwindows.org/) and Babun (with Mintty) on
ConEmu, which I should try too.

I must add that both Mintty and ConEmu authors are very responsive and helpful,
I really admire their work. Same for Babun author, he did a great job. Anyhoo,
all these tools do make life so much better at work so thanks again for
everyone involved in these prjects <3.

There are new options in newer windows environments but the progress is
certainly not evenly distributed. So here's my writeup for the eight people who
might come looking for some solace on windows within corporate barbed wires.
