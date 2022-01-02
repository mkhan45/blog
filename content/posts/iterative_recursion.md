---
title: "Emulating Recursion with Iteration"
date: 2021-12-20
# weight: 1
# aliases: ["/first"]
tags: ["functional-programming", "programming-languages"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: true
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/iterative_recursion"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
#cover:
#    image: "<image path/url>" # image path/url
#    alt: "<alt text>" # alt text
#    caption: "<text>" # display caption under cover
#    relative: false # when using page bundles set this to true
#    hidden: true # only hide on current single page
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---

TODO: Intro - something about why this is actually interesting,
since in pretty much every case it's slower and harder to read
than just using recursion

# Python Pattern Matching Overview

In most of the following examples, I use 
[Python 3.10's new pattern-matching feature](https://www.python.org/dev/peps/pep-0636/).
If you already understand pattern matching, feel free to skip this section.

TODO: summarize pattern matching features used in the examples.

# Tail Recursion

# Simple Argument Stack

The most well known way of emulating recursion through iteration is using a stack.
Here's an example with the fibonacci sequence:

```python
def fib_stack(n):
    arg_stack = []
    accumulator = 0

    while len(arg_stack) > 0:
        arg = arg_stack.pop()

        if arg < 2:
            accumulator += 1
        else:
            arg_stack.append(arg - 1)
            arg_stack.append(arg - 2)
    
    return accumulator
```

This approach is easy to generalize but it hardly has any benefits. 
It is generally slightly faster than the basic recursive approach since it 
eliminates function call overhead, but the stack has to be allocated anyway so it 
mostly cancels out. In Python, it's somewhat significant, but in compiled
languages it is definitely not worth the added complexity.

# Multi-Recursive Stack

Many interesting recursive functions recur on their arguments.

For example, the ackermann function:
```py
def ack(m, n):
    match (m, n):
        case (0, n): return n + 1
        case (m, 0): return ack(m - 1, 1)
        case (m, n): return ack(m - 1, ack(m, n - 1))
```

The last case can't be written with the previous method. What would we push to
the stack to recur on? The solution to this is pretty neat. Taking inspiration
from an actual call stack, we can push each argument separately.

```python
def ack_stack(m, n):
    stack = [m, n]

    # If the stack has 2 or more arguments,
    # apply the function
    while len(stack) >= 2:
        # stack looks like: [..., m, n]
        n = stack.pop()
        m = stack.pop()

        match (m, n):
            case (0, n): 
                # Push the final result onto the stack,
                # just like an interpreter VM might do
                stack.append(n + 1)
            case (m, 0):
                # Push the arguments onto the stack,
                # the function will be applied again
                # since there's more than one.
                stack.append(m - 1)
                stack.append(1)
            case (m, n): 
                # After this, the stack looks like:
                # [..., m - 1, m, n - 1]
                # Once we apply the function again, it will
                # look like:
                # [..., m - 1, ack(m, n - 1)]
                # This is exactly what we're looking for.
                stack.append(m - 1)
                stack.append(m)
                stack.append(n - 1)

    return stack[0]
```

As it is, this approach still has some significant limitations. What if a
function needs to recurse on multiple arguments? For example, this weird
contrived function:

```python
def f(a, b):
    if a <= b:
        return b
    else:
        new_a = f(a - 1, b + 1)
        new_b = f(a, b * 2)
        return f(new_a, new_b)
```

Since we're relying on the next iteration of the loop to apply the function
to the stack, we can't write the second case with exactly the same structure:

```python
def f_stack(a, b):
    stack = [a, b]

    while len(stack) >= 2:
        b = stack.pop()
        a = stack.pop()
        if a <= b:
            return b
        else:
            # what here?
            new_a = # ???
            new_b = # ???
            stack.append(new_a)
            stack.append(new_b)
```

As it turns out, this isn't a trivial transformation to make. With our stack,
we emulated a flat call structure where each function call only had one possible
continuation. For this, we need to emulate an entire call graph.

# Call Graph

This is the first method which can be generalized to any recursive function.

# Continuation Passing Notation (autoconversion from recursion to iteration)

An interesting aspect of the execution graph above is that it is directed. We pass
the result of each calculation back to its caller. This is a type of continuation,
we're essentially telling the computer what to do with the computed value. As it turns
out, making continuations explicit is useful in compilers.
