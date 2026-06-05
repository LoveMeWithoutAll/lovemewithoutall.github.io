---
layout: single
title: learning http/2
date: 2021-03-06 19:16:30.000000000 +09:00
type: post
header:
    teaser: "https://image.yes24.com/momo/TopCate0001/kepub/X_861924.jpg"
    image: "https://image.yes24.com/momo/TopCate0001/kepub/X_861924.jpg"
categories:
- IT
tags: [http]
---

When reading developer job postings, you often come across this kind of preferred or required qualification.

'Understanding of the HTTP protocol'

In the past, every time I came across this, I had a question. What level of understanding does it actually refer to? If you're a web developer, HTTP is a technology you use every single day, as a matter of course. So there isn't a web developer who doesn't know how to use it. Does it then mean they require an understanding of the technology down to the low level? If it's a matter of server configuration, in Spring Boot you can apply http/2 with just a simple setting in application.properties. IIS even supports http/2 without any special configuration at all(!).

But in order to optimize a web application, an understanding of http is absolutely necessary. Optimization isn't just about loading resources quickly. You need to make decisions about what to load first and what to show the user first. And to implement this series of decisions, you also need to understand how the protocol works and how it can be handled. Methods for web application optimization based on this kind of understanding of the http/2 protocol are called http/2 strategies. In other words, it's a matter of how to handle request-response multiplexing, server push, and prioritization.

Thanks to this book, I've been able to start thinking more deeply about http/2 strategies, which until now I had only known empirically and vaguely. Of course, this book alone isn't enough. It covers only a broad overview of http, and some of the tools it introduces are now old and no longer work. To understand it more deeply, further study is needed. Web development has somehow become a kind of art form. There's truly a long way to go to become a good developer.

20210306
