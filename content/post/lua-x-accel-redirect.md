---
date: 2016-06-07T20:43:56+05:30
title: "Using X-Accel-Redirect with lua"

---

I had used X-Accel-Redirect with Python/Flask and nginx at work. I wanted to do the same with lua for a hobby project.
So the title is what I Googled for when I wanted to serve files with nginx-lua.

I soon realized I was looking for the wrong thing. My search term was wrong. Hence the title is misleading too :D.
You can't actually use X-Accel-Redirect *meaningfully* with nginx-lua.
X-Accel-Redirect is apparently only processed by modules like ngx_proxy, ngx_fastcgi etc.

**Little bit about X-Accel-Redirect for the uninitiated**

X-Accel-Redirect is how nginx implements the feature you might be familiar with as X-Sendfile.
It's used to implement controlled file serving. For example if you want to let 
only the authenticated users download files, X-Sendfile is your friend 
(You realize you can not reveal the direct url to the file even for authenticated users right? Never trust the user etc.) 

**Back to the topic..**

So the best way to do this is to use [ngx.exec](https://github.com/openresty/lua-nginx-module#ngxexec). With ngx.exec you can do an internal redirect.
If you read the [x-accel](https://www.nginx.com/resources/wiki/start/topics/examples/x-accel/) 
documentation and utilize ngx.exec accordingly you can achieve the expected behavior.
But you read enough documentation for nginx-lua stuff ;) so here goes.

<pre>
server {
    listen 80;
    server_name _;

    location /root {
        content_by_lua '
            ngx.exec("/files-root/example.txt");
        ';    
    }

    location /files-root {
        internal;
        root /opt; # will serve /opt/files-root/example.txt
    }

    location /alias {
        content_by_lua '
            ngx.exec("/files-alias/example.txt");
        ';    
    }

    location /files-alias {
        internal;
        alias /opt; # will serve /opt/example.txt
    }
}
</pre>

That's that.

I have been hacking with nginx-lua on and off and I seem to come back to it when I'm miserably bored.
This stuff is pretty interesting. Or it probably gives me a false sense of achievement or whatever.
If you want to try out nginx-lua I have put together [something](https://github.com/chanux/dockerfiles/tree/master/rusty) which you can start with.
Currently I'm working on something more flexible than that, someting I can base my hobby nginx-lua projects on. I'll definitely report if I ever finish it.

Maybe follow me on [twitter](https://twitter.com/chanux). Who am I to say you **should**?

PS: Tweet at me if you got the should joke. :D
