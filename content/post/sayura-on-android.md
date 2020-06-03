---
title: "Sayura on Android"
date: 2020-06-01T14:19:42+08:00
draft: false
---

**TLDR**: Something I built had issues due to browser/platform quirks.
`keypress` (deprecated), `keydown`, `keyup` are not supported on browsers on
Android. The next option `input` event behaves differently on mobile platforms.
Firefox on Andorid emits `input` event for soft edits too.

Sayura is a project I built to scratch my own itch which is not being able to
type Sinhala anywhere. It was done in a time we had worse support for Sinhala
input than it is now. It was quite simple, lightweight and was nothing fancy.
But it was good enough to write short Sinhala phrases. Ideal for tweets,
which is the only thing that really matters anyway.

I always had to go to the Github page and click demo link to use Sayura. That
was until I got the bright idea of bookmarking it, recently. But I wouldn't
have my bookmarks everywhere. It's always better to have a nice url for it.
That's why I decided to get one with [Vercel](https://vercel.com/). Vercel
makes modern web app deployment a breeze. And most importantly,  it gives you
a nice, short URL.

So I just tried vercel, which turned out to be an excellent experience and got
my nice url. Proud of this achievement, I shared this on Twitter and went on
with my life. I didn't expect the many replies pointing out that my beloved
hobby project is broken.

And then I checked it myself. It worked on iOS (which I tried it on at first)
and I promptly relayed this info back to realize that it does not work on
Android. Wanting to make sure for myself I tried it on Chrome on Android.
Broken. On Firefox Broken. And then this distant memories of me realizing this
very fact, lazily trying to fix it and realizing it ain't worth the effort
creeped in. Oh well.

Luckily this time, I had more energy and drive to look deeper in to it. There
were people trying to use it but failing. It's probably worth fixing it.

So the problem is (or, problems are) `keypress`, `keydown` and `keyup` events
([KeyboardEvent](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent))
are not supported on Android while on iOS it still works (I generalized the
issue down to OS, hope no one gets cross).

So I sat down and updated the thing to use `keydown` event instead `keypress`,
which is deprecated. This does not fix Android issues, just makes me feel good.
Made sure this did not break things and moved on to the actual issue.

Since Android does not support `keydown` and `keyup` events, the next option
was to use `input` event
([InputEvent](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/input_event)).
I have feint memories of coming to this conclusion and being discouraged for
some reason. But I was more convinced to give it a try as my friend with much
more experience battling the frontend (Hey Te-O!) confirmed that `input` is
the way to go.

Then came the caveats. Chrome on Mac `InputEvent.data` would have the character
for the key that is pressed. Chrome on Android input event sends full string on
`InputEvent.data` per each key press up until a space key press (The string
resets on space key press). After coming to terms with this reality, with
the help of reasoning from my friend on why it has to be this way for soft
keyboards, I just put in the work and got it working. Then went to check it
on Firefox just make sure that I'm finally done.

Life my friend is not that simple on the frontend. Firefox does contain the
full string in `InputEvent.data` similar to Chrome. Except it
fires duplicate events. I have tried a few de-duplication hacks but it mostly
made things worse.

I searched about this and found a 5 year old, unresolved bug report on the
issue. A bit disheartened, I went to on to figure out a good de-duplication
strategy. So I started with a blank function to handle the `InputEvent` and
`console.log()`-ed the event like a peasant. And BAM! there are no duplicates.
It didn't take me a second to realize that these duplicates are a product of my
own soft edits (I programmatically update English letters to Sinhala letters)
to the text. So this is likely, not really a bug. Just a side effect of how
Firefox implements it. Since I have chose to die on edit in-place hill, I have
gotta find a solution to this quirk.

As always, brute force brought me to a decent-ish solution. Nothing I'm
especially proud of but hey, it works.

During this whole process I was quite angry at 'the state of things' on web.
Isn't it almost like the web is NOT a unifying platform anymore? But once
I learned why things have to be the way they are I statrted to
[appreciate](https://ferd.ca/complexity-has-to-live-somewhere.html) 'the
complexity if trivial things' more. The ride was quite educational and
humbling. 

I also tried to asses the Google Lighthouse score for Sayura page and make the
suggested improvements. Hope it will help more people find the site and
probably makes for better usability. Speaking of better usability, I could do
much better than just serving the demo page I originally created. But that
would require me to work with this scary technology called CSS. Trust me I've
tried it before and still have PTSD. But I'll eventually brave up and get to
it.
