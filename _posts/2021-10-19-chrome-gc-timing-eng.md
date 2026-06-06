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

1. GC is complex, and it keeps changing every time Chromium is updated. GC is mostly done incrementally, some of it concurrently, and some of it in parallel. And it's determined(?) by the allocation amount(?) and idle time.
	1. The summary is that most of the GC work is done incrementally, some concurrently, some in parallel; it's driven mostly by allocation amount and idle time.
	2. https://groups.google.com/a/chromium.org/g/chromium-discuss/c/eiO7Qxfyefk

20210903
