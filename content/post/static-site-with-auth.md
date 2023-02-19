---
title: "Static Site With Authentication"
date: 2023-02-19T10:34:12+08:00
---

Have you ever wanted to just make a static website and throw it behind an
authwall? Maybe to make your personal notes, playbooks that are too specific
and not very helpful to share publicly available to you over the interwebs. Or
else to use the static site system that you know works well to manage your
company knowledge base.

I have had both these problems and didn't really find a nice, straightforward
enough system. After looking around for YEARS, I ended up with this system that
works for me. Now I won't claim this is easy. But I made it work for my use
case.  However, there might be others that can benefit from this. Hence this
blog post, not an authwalled write up ;).

Below are the main ingredients of the system

- [Caddy](https://caddyserver.com/) - An easy to configure web server with free
  automatic TLS. All in a single binary.
- [oAuth2-proxy](https://github.com/oauth2-proxy/oauth2-proxy) - A proxy server
  that implements support for various oAuth providers (Github, Google, FB).
- [object-serve](https://codeberg.org/chanux/object-serve) - Custom software
  that can serve static files from a private GCS or S3 bucket.

I'd force compute in, a fourth ingredient, to make an analogy to Flour
water salt yeast[1], to show just how "easy" this is! I personally host all
these on a small cloud VM (run with systemd) and it's only occassionally
annoying. That too is because I have not automated the deployment. However,
I once automated[2] building/deploying the static site to object storage (GCS
in my case) and been happy with it ever since. I have not gone beyond the free
tier limits even with my aggressive (j/k) note taking.

This is the reason why I needed to figure out a way to serve content from
a *private* object storage bucket. In my search, I found [this blog
post](https://fale.io/blog/2018/04/12/an-http-server-to-serve-gcs-files) and
modified the code there to suit my needs.

object-serve is my second attempt at implementing this, now with support for
S3. I have hastily put it on Codeberg so anyone interested can take a look at
it. For anyone who noticed, I must tell you that I have tried my best to come
up with a good name but you see I failed miserably.

In a previous attempt, the whole deployment was automated with Google
Cloud Run and it was quite pleasant to work with. So that's an idea if you want
to take this further. However this is how I use mine today.

![Object Serve in action](/blog/images/static-site-with-auth/object-serve-in-action.png)

There's quite a bit of glue that holds this all together. I may polish up what
I have and put it online sometime. Feel free to figure it out by reading the
docs. It's good for you xD.

What I presented so far is just one way that ended up working for me. I'll note
down a couple other considerations I personally didn't get to work due to
"reasons".

Before I settled on oAuth-proxy for the oAuth based authentication,
I considered one of the caddy plugins[3] for the same task but failed to get it
going. One could probably achieve all this with just a couple of Caddy plugins
and have a system that's simpler to setup and might even provide better
performance.

Another option that caught my eye was this [writeup from
Cloudflare](https://blog.cloudflare.com/oauth-2-0-authentication-server/) on
how to host an oAuth server on Cloudflare Workers. I probaly got distracted
setting up node before I could do the `npm install` in the instructions.

- [1] https://kensartisan.com/flour-water-salt-yeast
- [2] https://chanux.me/blog/post/automate-static-site-publishing-on-gcp/
- [3] https://authp.github.io/
