---
layout: single
title: "Backward Compatibility Issues with iOS and Android WebViews"
date: 2024-06-28 09:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend]
---

Backward compatibility is always a thorny issue, and WebViews are no exception. As new versions of JS were released, situations arose where WebViews didn't support the new JS features. This problem was resolved as iOS and Android versions advanced, but there are still plenty of people in the world using older devices. Oddly enough, these users tend to be few in number yet generate enormous revenue (?), so they can never be taken lightly. So, drawing on my experience developing 11Kitties, I've put together a brief summary of how far you should support backward compatibility in WebViews.

1. esbuild
    1. Reason: esbuild supports es2015 and up. es2015 was a landmark version that introduced arrow functions, let, and so on, but Android Chrome didn't fully support es2015 even as late as 2017. By contrast, Safari surprisingly supported es2015 100% quite early on. If you don't like these constraints, you'll have to use the time-honored Babel.
    2. Support version
        1. Android Chrome: Used v69 for stable support
        2. iOS Safari: As appropriate
    3. References
        1. Trends in JavaScript from 2017 onward - JavaScript(ECMAScript) https://d2.naver.com/helloworld/2809766
        2. esbuild's official stance that it won't support es5: https://github.com/evanw/esbuild/issues/297#issuecomment-1670072906
2. PWA
    1. Reason: To use a PWA, you need to support globalThis. globalThis is not simply the window global object, which makes it quite tricky to write a polyfill yourself (see https://ui.toast.com/weekly-pick/ko_20190503). You either use a polyfill or give up some backward compatibility.
    2. Support version
        1. Chrome Android: 65
        2. iOS: 12.2
    3. Reference: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis
3. JS module syntax
    1. Reason: module import is practically a syntax you can't give up.
    2. Support version
        1. Android Chrome: It's supported from v61, but it showed unstable behavior such as blank-screen issues, so we used it starting from v64.
        2. iOS: Use it freely.
    3. Reference: https://babeljs.io/docs/learn/#modules

20240628
