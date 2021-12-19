---
title: "Java Hello World"
date: 2021-02-22
# weight: 1
# aliases: ["/first"]
tags: ["cool-code-snippets", "java"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/java-hello-world"
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

```java
class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
}
```

I'm sure everyone's seen Hello World in Java. In fact, I'd expect that a significant number of developers write it as their first program. It's always been funny to me how much about Java you have to know just to fully understand this program.

I'll go over it line by line:

___

## `public class HelloWorld`

This line declares the public class `HelloWorld`. Hopefully the file is called `HelloWorld.java`, or else it won't compile.

___ 

## `public static void main(String[] args)`

This declares the `public static void main` function with argument of type `String[]` `args`, and in my experience it's generally something CS novices copy paste around for the first few programs. There's quite a few important keywords.

- `public`: the method can be accessed from other files. I'm not completely sure why `main()` needs to be public, but there's probably a good reason.
- `static`: the method is part of the class as a whole, not an individual instance of the class.
- `void`: the method doesn't return anything
- `main`: the magic method name which makes it the entry point of the program
- `String[] args`: the argument is an array of `String`s called `args`. The args come from the command line.

___

## `System.out.println("Hello World")`

This line prints "Hello World" to the terminal. `System` is a global Java class, essentially a namespace which exists to provide static methods and fields, one of which is stdout, called `out`. `out` is a `PrintStream` which has an instance method `println()`.

___

To execute this code snippet, at least until fairly recently, you had to run:

```
javac HelloWorld.java
java HelloWorld
```

`javac` produces a class file which can be run by the JVM using the `java` command. When I saw this, the first question I asked was how I could create a proper executable file. My teacher didn't know but he didn't know much. I didn't learn about the JVM or bytecode or how it's different from assembly for at least a few months.

___

To summarize, you need to understand these Java concepts to understand `HelloWorld.java`:
- Classes
- Methods
- `public`
- `static`
- `void`
- `main`
- `String[]`
- Namespaces (`System`)
- `PrintStream`
- Instance methods
- Class files / bytecode

Of course that's a bit exaggerated, but there's definitely a lot of magic for someone writing their first few lines of code. I don't think Java's terrible for teaching CS, but I wish there wasn't so much that has to be handwaved away.
