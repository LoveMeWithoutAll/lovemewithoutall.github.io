---
layout: single
title: Functional JavaScript
date: 2024-11-19 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://contents.kyobobook.co.kr/sih/fit-in/458x0/pdt/9791162240427.jpg"
    image: "https://contents.kyobobook.co.kr/sih/fit-in/458x0/pdt/9791162240427.jpg"
categories:
- IT
tags: [javacsript]
---

It's been nearly ten years now since I started using lodash and underscore. I thought I was fairly familiar with functional JavaScript, even though I had never studied it formally. But after reading this book, I belatedly realized how arrogant I had been all along.

Simple function composition alone isn't enough to say you're really doing functional JavaScript. It seems that to truly do functional JavaScript, you have to wrap your functions completely in functors and monads, leaving no gaps, so that you don't even need to handle errors separately. If you ask whether that level of engineering is truly necessary, or whether it's over-engineering, the answer is yes, it can be. Functional programming is something you should use appropriately, only where it's needed, because declarative programming is quite hard to learn at first. Even so, there's a big difference between knowing it and choosing not to use it, versus not using it because you don't know it.

Meanwhile, I should immediately stop treating Java as some outdated, old-fashioned language. I've hardly ever seen code that uses getOrElse in JavaScript. But these days, it's rare to find Java code that doesn't use getOrElse. JavaScript has `?.` and `||`, but it's hard to call them equivalents for functional programming. At this rate, I wonder whether JavaScript itself might end up being the outdated language.

20241105
