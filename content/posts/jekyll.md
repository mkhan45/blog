---
title: "Jekyll"
date: 2020-05-13
# weight: 1
# aliases: ["/first"]
tags: ["blog"]
author: "Mikail Khan"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: false
description: ""
canonicalURL: "https://blog.mikail-khan.com/posts/jekyll"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: true
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---



I’ve decided to move everything to jekyll and maybe start writing stuff.
I made a simple script to insta start a new post because typing the date is tough:

```sh
#!/bin/sh

title=$1
date=`date +'%Y-%m-%d'`

nvim "$date-$title".md
```
