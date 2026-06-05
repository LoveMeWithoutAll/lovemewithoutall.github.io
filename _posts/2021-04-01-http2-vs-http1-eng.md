---
layout: single
title: HTTP/2 versus HTTP/1.1
date: 2021-04-01 12:16:30.000000000 +09:00
type: post
header:
    teaser: "https://developers.google.com/web/fundamentals/performance/http2/images/binary_framing_layer01.svg"
    image: "https://developers.google.com/web/fundamentals/performance/http2/images/binary_framing_layer01.svg"
categories:
- IT
tags: [HTTP]
---

Someone raised the question of whether we really have to use HTTP/2, so I compared the performance with Chrome Lighthouse. I ran the measurements against the same web app (an SPA developed with Vue.js).

This is with HTTP/2 applied
![http2](/assets/images/2021-04-01-http2-vs-http1/http2.png)

And below is with HTTP/1.1
![http1](/assets/images/2021-04-01-http2-vs-http1/http1.png)

Without doing anything at all, I gained 15 more points in performance.
