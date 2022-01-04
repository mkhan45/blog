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

The easiest and most useful method of converting recursion to iteration is
only possible when a function is tail-recursive. A function is called tail-recursive
when all of its recursive calls are tail-calls, i.e. they are the last computation
done by the function.

For example, this is the naive way to calculate factorial recursively:

```py
def factorial(n):
    if n == 1:
        return 1
    else:
        return n * factorial(n - 1)
```

This is **not** tail recursive. While at first glance, it might seem like
`factorial(n - 1)` is the last calculation in the function, it still has to 
multiply the result by `n`.

To turn this function tail-recursive, we must add an accumulator argument:

```py
def factorial(n, accumulator):
    if n == 1:
        return accumulator
    else:
        return factorial(n - 1, n * accumulator)
```

Now, `factorial(n - 1, n * accumulator)` is the last computation done in
the recursive case. Many recursive functions can be turned tail-recursive
similarly by adding an accumulator argument.

Generally, you would rewrite the function with an inner function so you
don't have to specify the accumulator every time:

```py
def factorial(n):
    def inner(n, accumulator):
        if n == 1:
            return accumulator
        else:
            return inner(n - 1, n * accumulator)

    return inner(n, 1)
```

I can already hear you asking why this matters. It's still not iterative,
the recursive call is right there!

As it turns out, tail-recursion is easily convertible to iteration. Since
the recursive call is the last computation of the function, we can just
reuse the stack frame without creating a new scope. It's just like starting
another iteration of the loop. In fact, one of the methods to convert tail calls
to iteration is to just convert the tail call into a GOTO. If Python had GOTOs,
it would look like this:

```py
def factorial(n, accumulator):
    LABEL Start
    if n == 1:
        return accumulator
    else:
        n = n - 1
        accumulator = n * accumulator
        GOTO Start
```

Many languages automatically convert tail recursion to iteration, especially
functional ones. This is called tail-call optimization, and there are many ways
to accomplish it. If you want to learn more, I recommend 
[this article](https://dotink.co/posts/tce/) by Linus Lee.

If you want tail-call optimization in a language that doesn't have it built-in,
you can use trampolines:

```py
class Thunk:
    def __init__(self, f, args):
        self.f = f
        self.args = args

    def compute(self):
        result = self
        while type(result) == Thunk:
            result = result.f(*result.args)

        return result

def factorial(n):
    def inner(n, acc):
        if n == 1:
            return acc
        else:
            return Thunk(inner, [n - 1, n * acc])

    thunk = inner(n, 1)
    return thunk.compute()
```

I won't try to explain it better than Linus, but, essentially, this 
puts the call stack into the `Thunk` instance and just collapses the
stack in a loop until it finds a final value.

This approach (without the bootleg trampolines) is really useful since it's 
a quick change that barely decreases the function's legibility. In many functional 
languages, this is how loops must be written so it becomes second nature to write functions 
this way. Unfortunately, it only works in specific cases.

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

This approach is easy to generalize to other trivial recursion but it hardly has any benefits. 
It is generally slightly faster than the basic recursive approach since it 
eliminates function call overhead, but the stack has to be allocated anyway so it 
mostly cancels out. In Python, it can be somewhat significant, but in compiled
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

# Continuation Passing Notation

In this section, we'll finally find a method that can automatically convert
an arbitrary recursive function to an iterative one.

An interesting aspect of the execution graph above is that it is directed. We pass
the result of each calculation back to its caller. This is a type of continuation,
we're essentially telling the computer what to do with the computed value. As it turns
out, making continuations explicit is useful in compilers.

---
---

If you liked this post and want to read more, sign up for my newsletter:

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
