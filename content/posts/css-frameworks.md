---
title: "CSS Frameworks"
date: 2020-05-25
# weight: 1
# aliases: ["/first"]
tags: ["web"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/css-frameworks"
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

I remember the first time I used Bootstrap. It was during the summer between 8th and 9th grade; I had a simple website that looked bad and I wanted it to look good, so I copy pasted some lines from the Bootstrap website and added some classes to some divs and it started looking better. Eventually I decided to restructure the HTML completely instead of just adding classes and I was completely blown away by the results. I've always had pretty terrible luck designing anything, so being able to easily make a site that looks good and works on phones was amazing.

Last year I took a web app development class. After we went through the basics of HTML/CSS/JS our teacher recommended using Bootstrap or Materialize but I decided to figure out what Bootstrap did to make sites responsive that I couldn't do myself. At this point, I'd pretty much forgot all the CSS I'd learned, so I messed around with some different CSS rules and eventually tried a flexbox. It worked pretty well, but then I found CSS Grid on [CSS Tricks](https://css-tricks.com/snippets/css/complete-guide-grid/).  I also found `@media` rules. Using these two, it's pretty easy to make a simple site that resizes nicely for mobile. I decided to use only my own CSS for all the webpages I made during that class, and things mostly turned out well. I still suck at choosing colors though.

Recently I've been writing a few more websites. The main new difficulty is that these sites needed a navbar, and I hadn't ever actually written a navbar that turns into a hamburger menu. I figured it out fairly quickly with some Javascript, which I realize now is unnecessary, but there was also a lot of CSS to compensate for other things changing, and given that I hadn't really written anything else with the navbar in mind, it got kind of messy.

I decided that for my next site I'd use a CSS framework, but a fairly minimal one that at the very least doesn't need any Javascript. The first few I found looked pretty nice, but they all had one major problem. I tested all of them by setting up a simple site with a header/navbar and a footer with a paragraph of lorem ipsum text in the middle. If any of them didn't work just by adding some stylesheets and adjusting some classes and maybe some minimal html changes for the navbar, I didn't consider them any further.

The framework I ended up using is [boba](https://www.buildwithboba.com/). It's somewhat less minimal than I'd like, but it's still only ~22kb gzipped and doesn't need any Javascript. Given that I suck at choosing colors, it's nice boba does come with a really nice default selection. It also comes with a nicely animated hamburger menu. Other than that, it's got a few components and a grid system based on flexbox. It's true that most websites using boba probably look pretty similar if you don't add some custom css, but that's to be expected.

Overall, I'm really happy with boba. I'll probably still handwrite CSS for most websites I build, but it's definitely my goto when I do need a CSS framework. If I need something that does look significantly unique and I can't write my own rules on top of boba to get things how I want, I might have to find a different one, but given that I didn't search too hard for boba I'm sure there's plenty more good ones out there.

I'm also more interested in writing my own CSS framework than before. I'll probably have to learn SASS though. 
