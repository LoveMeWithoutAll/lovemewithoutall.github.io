---
layout: single
title: Visual Studio Code에서 2개 단어를 동시에 포함하는 파일을 검색하자
date: 2018-11-29 08:45:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/2d/Visual_Studio_Code_1.18_icon.svg/1200px-Visual_Studio_Code_1.18_icon.svg.png"
categories:
- IT
tags: [Tool]
---

[Visual Studio Code]로 단어 2개를 동시에 포함하는 파일을 검색해보자. 

1. 검색창을 띄운다(Ctrl + <Shift> + F)
1. 정규식 사용(Alt + R)을 체크한다
1. 아래와 같은 정규식으로 검색한다

`(word1[\s\S\n]*word2)|(word2[\s\S\n]*word1)`

## 참고
* https://code.visualstudio.com/updates/v1_29#_multiline-search
* https://stackoverflow.com/questions/49944569/search-in-vs-code-for-multiple-terms

[Visual Studio Code]: https://code.visualstudio.com/
