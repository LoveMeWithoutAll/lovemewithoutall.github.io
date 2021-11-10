---
layout: single
title: CSSOM 과 inline-style
date: 2021-11-10 19:14:30.000000000 +09:00
type: post
header:
    teaser: "https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/full-process.png"
    image: "https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/full-process.png"
categories:
- IT
tags: [css]
---

html parser가 inline style을 만나면 CSSOM을 만들기 위해 parser 동작을 일시 중지할까?
cssom은 javascript가 css style을 조작하기 위한 모델이다
 cssom은 외부 css를 받아오든, style tag를 만나든, inline style을 만나든 그 때마다 만들어진다. 렌더링 되기 전에, js가 실행되기 전에 만들어진다

html parser가 inline-style을 만날 때마다 cssom을 만들기 위해 잠시 blocking된다면, 그만큼 성능에 악영향을 끼치지 않을까?
명확한 답이 안나오네

https://stackoverflow.com/questions/64549997/is-cssom-created-only-when-the-html-page-has-link-for-external-sheet-or-is-it-ev

20210927
