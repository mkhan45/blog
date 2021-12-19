---
title: "REPL"
date: 2021-03-08
# weight: 1
# aliases: ["/first"]
tags: ["cool-code-snippets", "programming-languages"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/repl"
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

This one is really simple. Most people are familiar with REPLs for Python, JavaScript, or even repl.it but the term comes from Lisp.

REPL stands for Read-Eval-Print-Loop, and here's how you make one in Common Lisp:

```clisp
(loop(print(eval(read))))
```

It's pretty easy to tell what it does; it just `read`s from stdin, `eval`s the input, `print`s the evaluation, and `loop`s infinitely. It's a pretty ugly REPL though since it doesn't add a newline after the output:

```
~ Δ clisp repl.cl
(+ 3 5)

8 (* (+ 3 5) 12)

96
```

It can be made a lot nicer with a little work:

```clisp
(defun println (s)
  (format T "~s~%" s))

(defun readprompt (p)
  (format T p)
  (read))

(loop(println(eval(readprompt "> "))))
```

```
~ Δ clisp repl.cl
> (+ 5 3)
8
> (* 3 (+ 42 (* 3 2)))
144
>
```

___

For reference, here's a simple REPL in Python:

```py
while True: print(eval(input()))
```

```
~ Δ python repl.py
5 + 5 * 3
20
[x**2 for x in range(10)]
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

And to make it print a prompt it's pretty simple:

```py
while True: 
    print("> ", end="")
    print(eval(input()))
```

```
~ Δ python repl.py
> 5 + 3 * 2
11
> [x**3 for x in range(15) if x % 2 == 0]
[0, 8, 64, 216, 512, 1000, 1728, 2744]
>
```
