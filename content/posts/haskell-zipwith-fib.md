---
title: "Infinite Fibonacci List with Haskell ZipWith"
date: 2021-02-16
# weight: 1
# aliases: ["/first"]
tags: ["cool-code-snippets", "haskell", "functional-programming"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/haskell-zipwith-fib"
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

For my first post in the cool-code-snippets series I'll write about one of my favorite pieces of code: 

```hs
fibs = 1 : 1 : zipWith (+) fibs (tail fibs)
```

This Haskell oneliner makes an infinite, lazily calculated list of all the fibonacci numbers. Since it's infinite and lazily calculated, printing it just prints the fibonacci sequence as fast as the computer can calculate them, and it makes a pretty interesting pattern of continually growing arcs from left to right.

![](https://mkhan45.github.io/cool_code_snippets/resources/fibonacci_arc.png)

Working with the list is pretty convenient:

```hs
first_ten_fibs = take 10 fibs
even_fibs = filter ((==0) . (`rem` 2)) fibs
squared_fibs = map (^2) fibs
```

___

This snippet will look pretty alien if you don't know Haskell, but it's pretty simple once you understand its components.

### Prepending with `:`

In Haskell, `:` prepends to a list. More accurately, it returns a new list with the element prepended.

```hs
> ls = 1 : [5, 10, 15]
> ls
[1,5,10,15]

-- you can compound it:
> ls = 1 : 5 : [10, 15]
> ls
[1, 5, 10, 15]

-- it needs to be applied to a list:
> ls = 1 : 2 : 3 -- type error since 3 is not a list
```

That takes care of the first part. `1 : 1 : (...)` means that the list will start with [1, 1]. This also means that `zipWith (+) fibs (tail fibs)` is a list.

### `zipWith`

`zipWith` should be pretty familiar to anyone familiar with functional concepts from languages like Python or Rust. The syntax for `zipWith` is `zipWith (function) (list A) (list B)`. All it does is iterate over the lists in parallel and apply the function to the elements.

In Python, for example, `zipWith` can be implemented like this:

```python
def zipWith(f, l1, l2):
    return [f(a, b) for (a, b) in zip(l1, l2)]
```

Keep in mind that `zip` (in Haskell and Python) turns two lists `[a0, a1, ...]` and `[b0, b1, ...]` into `[(a0, b0), (b0, b1), ...]`.

So now that we know how `zipWith` works, let's look at its arguments:

- `(+)` is the function
- `fibs` is the first list
- `tail fibs` is the second list

`(+)` definitely won't look like a function if you're not familiar with Haskell. In Haskell, operators are just infix functions, and you can turn them into prefix functions by surrounding them in parentheses. That means that `(+)` is just a function which takes two arguments and returns their sum.

`fibs` might also seem pretty strange as an argument, it's what we're trying to define. This works because variables are actually just functions in Haskell, so they can be recursive. For example, this is perfectly valid Haskell:

```hs
> a = a
```

In this case, `a` never evaluates to anything; it's a recursive function without a base case.

So when Haskell comes across `fibs` as the first argument of `zipWith`, it just evaluates it. It's the same thing with `tail fibs`. `tail` in Haskell just returns a list containing all but the first element of the list:

```hs
> ls = [1, 2, 3, 4]
> tail ls
[2, 3, 4]
```

Let's trace what Haskell tries to do when you want to evaluate `fibs`, element by element

```
Element 0: 1
Element 1: 1
    These two were defined at the start with `1 : 1 : ...`

Element 2: 2
    zipWith (+) fibs (tail fibs) =
        fibs:        [1, 1, ..]
                      +  +
        (tail fibs): [1, ..]
        =            [2, ..]

Element 3: 3
    zipWith (+) fibs (tail fibs) =
        fibs:        [1, 1, 2, ..]
                      +  +  +
        (tail fibs): [1, 2, ..]
        =            [2, 3, ..]
    The important bit here is that we get to use the element that was calculated in the previous step

Element 4: 5
    zipWith (+) fibs (tail fibs) =
        fibs:        [1, 1, 2, 3, ..]
                      +  +  +  +
        (tail fibs): [1, 2, 3, ..]
        =            [2, 3, 5, ..]
    And so on
```
