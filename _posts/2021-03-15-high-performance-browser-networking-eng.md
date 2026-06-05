---
layout: single
title: (Lessons from a Google Engineer) Networking and Web Performance Optimization Techniques
date: 2021-03-15 12:16:30.000000000 +09:00
type: post
header:
    teaser: "https://mobile.kyobobook.co.kr/common/image/resize?url=http://image.kyobobook.co.kr/images/book/large/659/l9788966261659.jpg"
    image: "https://mobile.kyobobook.co.kr/common/image/resize?url=http://image.kyobobook.co.kr/images/book/large/659/l9788966261659.jpg"
categories:
- IT
tags: [network]
---

This happened a few years ago. The battery on the Android phone I was using at the time was draining strangely fast, so I went to the manufacturer's service center. It was a model whose battery had already become a known issue. So naturally, I assumed my phone also had a battery problem. The repair technician at the service center quietly listened to my explanation along these lines, then inspected the device. And then he said this to me.

'The battery seems fine, but it says this app is using an excessive amount of the device's battery.'

And then he showed me which app was draining my phone's battery so quickly. Surprisingly, that piece-of-junk app was one I had made myself. Since it was a work app, it had over 20,000 users. When I looked into the cause, I found it was calling the network and GPS through short-interval polling. It's a foolish anecdote from my early days as a mobile app developer.

So if you ask me how much better I've gotten compared to back then, honestly it's hard to give a clear answer. Since I mostly develop web applications, I've neglected my understanding of the technologies that make up the network. No matter how much the web browser handles things on its own, how could I have been so ignorant?

These days, I feel like I'm standing at a fork in the road regarding what kind of developer I want to become. "Good developers know how things work. But great developers understand why they work the way they do. (quoted from the book's preface)" Can I move beyond being a shoddy developer to become a good developer, even a great developer? At the very least, thanks to this book, I think I can take the first step toward developing while thinking about protocols and latency. It was hard and challenging to read throughout, but it's a book I'm grateful for in many ways.

20210314
