---
layout: single
title: Chrome GC timing
date: 2021-10-19 18:35:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn.pixabay.com/photo/2017/07/12/14/07/trash-2497061_1280.jpg"
    image: "https://cdn.pixabay.com/photo/2017/07/12/14/07/trash-2497061_1280.jpg"
categories:
- IT
tags: [chrome]
---

1. GC는 복잡하고, 크로미움 업데이트 할 때마다 계속 바뀐다. GC는 대부분 증분적으로, 일부는 동시에. 일부는 병렬적으로 한다. 그리고 할당량(?)과 유휴 시간에 결정(?)된다
	1. The summary is that most of the GC work is done incrementally, some concurrently, some in parallel; it's driven mostly by allocation amount and idle time.
	2. https://groups.google.com/a/chromium.org/g/chromium-discuss/c/eiO7Qxfyefk

20210903
