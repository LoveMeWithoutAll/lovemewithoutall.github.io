---
layout: single
title: Search for Files Containing Two Words at Once in Visual Studio Code
date: 2018-11-29 08:45:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
categories:
- IT
tags: [Tool]
---

Let's search for files that contain two words at once using [Visual Studio Code].

1. Open the search box (Ctrl + <Shift> + F)
1. Check "Use Regular Expression" (Alt + R)
1. Search with a regular expression like the one below

`(word1[\s\S\n]*word2)|(word2[\s\S\n]*word1)`

## References
* https://code.visualstudio.com/updates/v1_29#_multiline-search
* https://stackoverflow.com/questions/49944569/search-in-vs-code-for-multiple-terms

[Visual Studio Code]: https://code.visualstudio.com/
