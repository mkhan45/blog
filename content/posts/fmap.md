---
title: "Representing Data Structures with First Class Functions"
date: 2021-10-11
# weight: 1
# aliases: ["/first"]
tags: ["functional-programming", "data-structures"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/fmap"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---

I was talking to my roommate about how all data structures can be emulated through
function compositions the other day so I thought I'd prove it.

One of the most versatile data structures is a map, and a map is practically a function already.
Sometimes, when we talk about functions, we talk about how they map their
domain to their range.

Here's a simple map as a function:

```plain
map = {'a': 1, 'b': 2, 'c': 3}

# Representing the map as a function f:
f('a') = 1
f('b') = 2
f('c') = 3
```

This is all pretty straightforward in mathematical notation, but obviously
this isn't real code.

___

&nbsp;

I'll start with Python. An empty map is pretty easy to represent as a Python function:

```
empty_map = lambda _: None
```
It's just a function which takes any value and returns None.

It's also pretty easy to create a one variable map as a Python function:
```
simple_map = lambda x: 1 if x == 'a' else None
```

But how do we go about adding variables to this map? It's easy, just add an if statement:
```
new_map = lambda x: 2 if x == 'b' else simple_map(x)
```

If we take `new_map('b')`, it evaluates right to 2. If we take `new_map('a')`, it
evaluates `simple_map('a')`, which is just 1.

Now we can draw the rest of the owl:
```py
empty_map = lambda _: None

def insert(m, k, v):
    return lambda fetched_key: v if fetched_key == k else m(fetched_key)

def remove(m, k):
    return lambda fetched_key: None if fetched_key == k else m(fetched_key)
```

To insert a key into an existing map function, we just add a new 
if statement to the recursion stack. I think it's something that's simpler
to understand through code than English. A diagram would probably be helpful but
I'm bad at drawing.

___

&nbsp;

Using this code is pretty straightforward:
```py
m = empty_map
m = insert(m, 1, "a")
print(m(1)) # a

m = insert(m, 2, "b")
m = insert(m, 3, "a")
m = insert(m, 1, "c")
print(m(1), m(2), m(3)) # c b a

m = remove(m, 2)
print(m(1), m(2), m(3)) # c None a
```

If only Python had a pipe operator, this would look a lot nicer.

___

&nbsp;

It's pretty easy to make a map out of just functions. I think
it's evident that any other data structure can be emulated by a map,
not counting the performance characteristics. Of course, performance
is the one of the biggest factors in choosing a data structure 
(ergonomics/conceptual model is more important *sometimes*), otherwise 
we'd just write fancy interfaces over arrays. However, while this isn't directly 
useful, it has some cool implications.

Essentially, this is using the call stack to store data, which isn't
uncommon in recursive algorithms though this is an extreme version of it. 
You could have an "empty" map which takes up a lot of space using this method 
if you just kept adding and removing the same element. This isn't completely a bad
thing. It makes this map what's known as a [persistent data structure](https://en.wikipedia.org/wiki/Persistent_data_structure); each version of the map is stored without having to deep copy the data. 
This is very useful in functional languages where mutation isn't possible. 
For example, persistence is the main advantage of Jane Street's OCaml 
[`Base.Map`](https://ocaml.janestreet.com/ocaml-core/v0.13/doc/base/Base/Map/index.html)
over [Base.Hashtbl](https://ocaml.janestreet.com/ocaml-core/v0.12/doc/base/Base/Hashtbl/index.html).

An interesting thought is that while the concept of the "call stack" is a
computer science term, it also "exists" in any standard algebraic notation, 
we just don't call it that.

Another important implication is that use of functions can sometimes be 
replaced by different data structures. For example, recursive algorithms can 
often be optimized in languages without tail-call optimization by using a stack.

___

&nbsp;

If you liked this post, sign up for my newsletter:

{{< rawhtml >}}
<form style="margin: auto" method="post" action="https://listmonk.mikail-khan.com/subscription/form" class="listmonk-form">
    <div>
        <p><input type="text" name="email" placeholder="E-mail" />
        <input type="text" name="name" placeholder="Name (optional)" />
        <input id="edc6e" type="checkbox" name="l" checked value="edc6e7b1-7f43-4773-b49b-6fb9fff48df4" />
        <label for="edc6e">Blog Posts</label>
        </p>

        <p><input type="submit" value="Subscribe" /></p>
    </div>
</form>
{{< /rawhtml >}}
___

&nbsp;

*If you learned something, consider hiring me. I'm a sophomore at Purdue
looking for a Summer/Fall 2022 internship. I'm generally interested in
functional programming, programming languages, compilers, systems development, 
graphics, and simulators. Find my resume and portfolio at <https://mikail-khan.com>*
