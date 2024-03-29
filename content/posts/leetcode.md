---
title: "Leetcode"
date: 2020-06-01
# weight: 1
# aliases: ["/first"]
tags: []
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/leetcode"
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


I've written a number of reasonably sized projects, so I think I understand the basics of architecting a program. Figuring out how all the components of a program work together is one of the most fun parts of writing a program for me, which is part of the reason that I always start projects without finishing them. Implementation details are annoying and tedious.

I've never really looked into competetive programming because I haven't really liked writing algorithms for the purpose of writing algorithms, but recently I've tried Leetcode and Codeforces and been surprised at how bad I am at them. A few days ago, I started going through some problems and while I haven't *really* enjoyed them, I've solve them to learn. I much prefer Leetcode to Codeforces because all of the Codeforces problems seem more about the math than about the coding. I can usually solve easy problems from each in twenty or so minutes, and the only medium difficulty problems I've solved didn't take long because I'd already known where to start from somewhere else. I haven't tried any hard problems yet.

Today, I was working on an easy problem and found myself struggling. It's [#70 on Leetcode, Climbing Stairs](https://leetcode.com/problems/climbing-stairs/). I wrote out the first 7 or 8 steps and found an equation for each step, unfortunately it used factorials so it was easily O(n^2), and as it turns out factorials get really big so it couldn't calculate anything above n=30 or so. When I get stuck I've found that the discussion titles can give good hints without giving away the problem, so I decided to peek at them. One title stood out: "Basically it's a fibonacci". 

Of course, the naive way of calculating fibonacci is ridiculously slow, and there's a slightly less naive way where you can store the recursion, but the simple iterative solution is way faster. Just iterate and keep adding:

```c
int fib(int n) {
   int a = 1, b = 1;
   for (int i = 0; i < n; i++) {
      int c = b;
      b += a;
      a = c;
   }

   return b;
}
```

While fibonacci is defined recursively, it can be done iteratively without using a stack because:
- to calculate a term you don't need the value of any succeeding terms
- you only need the previous two terms

The stair problem is the same. To get to step n, you can take one step up from step n - 1, or you can take two steps from step n - 2. To get the total number of steps to get to n, just add the total number of steps needed to get to n - 1 and n - 2. This is essentially recursive and is literally just fib with a different starting point: s(n) = s(n - 1) + s(n - 2); s(1) = 1, s(2) = 2.

With that, the solution is simple:
```c
int climbStairs(int n){
    if (n == 1) {
        return 1;
    } 
    
    int a = 1, b = 2;
    
    for (int i = 2; i < n; i++) {
        int c = b;
        b += a;
        a = c;
    }
    
    return b;
}
```

I had to inelegantly add an if statement and adjust the bounds of the for loop to make it easier to think about, but there's definitely a way to do without.

I'm simultaneously ashamed that I couldn't figure this out without a hint and amazed at how simple the solution was. I might actually have fun solving more problems now.
