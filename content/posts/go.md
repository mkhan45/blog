---
title: "Go"
date: 2020-05-15
# weight: 1
# aliases: ["/first"]
tags: ["web", "go"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/go"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
---

I decided to buy [mikail-khan.com](https://mikail-khan.com) a few days ago. I didn't really have anything in mind for it, but I thought I'd set up a simple server and do whatever fun stuff is possible within AWS free tier usage limits. 

Of course, I had to decide what do use for the server backend. Given that I want to stay within free tier usage limits, any Python framework seemed like a bad choice if I want to do anything remotely intensive. Anything JVM based also seemed questionable because of memory constraints. That basically narrowed down my choices to: something on C/C++, something with Rust, or Go. I didn't really want to deal with memory leaks so I also didn't consider C that much. 

I've never used Go but I have a friend who's much more experienced with Web/Server development who knows Go and recently started learning Rust, so I asked him. Even though he's admitted to really liking Rust, he, without any hesitation, recommended Go.

To be honest, I expected not to like Go. I didn't like the syntax (mainly for structs declaration/initialization) when I briefly tried it a few months ago, and I still don't. I also don't like the lack of functional programming stuff, enums, and just features in general (I still like C though; it's easier to get into the mindset of not needing features in C).

As it turns out, I'm pretty happy with Go for my server. Having an http server package in the standard library does a lot. A Hello World was super easy, and adding a bit more was too. Other stuff like http redirecting to https was easily done using `go http.ListenAndServe(":80", http.HandlerFunc(http_redirect))`, green threads in general are pretty cool.

I do still have to have a browser open to write simple stuff with Go, but it's been very simple to learn. A lot of Stack Overflow answers are pretty outdated, but they're enough to point me to which docs I should check out. The docs are pretty nice. 

The biggest benefit for me though, is the compilation speed. Go compiles quickly and easily enough that I can compile on my AWS server without wrecking my CPU credits and given that there's also typechecking etc, iteration time is easily faster than it would be in Python or Javascript.

There are still some problems though. The most annoying thing I noticed translating a simple Python Flask server is that in the default `net/http` library url path matching is quite limited.

In the Flask server, here are a some routes for a multiplayer game of Pong:
- `@app.route('/room/<num>/initiator')`
- `@app.route('/room/<num>/observer')`
- `@app.route('/room/<num>/signal',methods=['POST'])`

There's 4 more of them.

All of them start with `/room/`, assign a variable `num`, and then there's an additional specifier for an action. Go doesn't have any way to assign a separate handler function to each of these routes without writing your own additional routing function. It would be much easier if the routes were like `/room/initiator/<num>` etc, but it doesn't make as much sense semantically.

To work around this, I have to have a giant switch statement:
```go
switch ir {
case "initiator":
        tmpl, err = template.ParseFiles("templates/pong/initiator.html")
case "observer":
        tmpl, err = template.ParseFiles("templates/pong/observer.html")
case "signal":
        pong_signal_handler(w, r)
        return

...

```

As far as I understand, there's plenty of Go frameworks that solve this issue, but given that Go is practically a server framework by itself, I feel that there's more drawbacks to using an additional framework than benefits for now. This is the biggest problem with adding so much stuff to the standard library: you can't add too many features. Rust's standard library is way smaller, but given Rust's otherwise large feature set, code written using a framework like `actix-web` or `rocket` has a lot of niceties that you don't get with Go.

Honestly though, at least for me, the tradeoffs are definitely worth it. I'll keep using Go for my server for the near future; I can't see any reason I'd want to switch soon. Most likely, even if I want some more features, I'll switch to another Go framework. The near instant compile times are really nice.
