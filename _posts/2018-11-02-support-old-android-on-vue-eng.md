---
layout: single
title: Running a Vue.js WebView on Android KitKat and Below
date: 2018-11-05 14:03:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/support-old-android-on-vue.png"
    image: "/assets/images/support-old-android-on-vue.png"
categories:
- IT
tags: [android, Vue.js]
---

## The Incident

I'm in charge of an Android hybrid app, and not long ago I applied [Vue.js] to one particular page. I thought it was running smoothly, but this afternoon a bug report came in. When I checked, the JavaScript in the part where I had applied [Vue.js] wasn't running. The details are as follows.

* Device where the error occurred: LG GPad 8.0 LTE
* Android version: KitKat 4.4.2

At first glance, I suspected that maybe the JavaScript wasn't executing properly because of the old Android version. Fortunately, [remote debugging of Android devices](https://developers.google.com/web/tools/chrome-devtools/remote-debugging) is possible from the KitKat version onward. I downloaded the [driver](https://www.lge.co.kr/lgekor/download-center/downloadCenterList.do#manual), plugged the device into my machine, and ran remote debugging. This error showed up.

![Uncaught Syntax Error on old android](/assets/images/support-old-android-on-vue.png)

### An <b>Uncaught Syntax Error</b>, of all things!!!

## Debugging

1. Suspect 1: [Vue.js]

    The first thing I suspected was [Vue.js], since maybe [Vue.js] doesn't support the WebView of old Android versions. So I searched around and found this answer. It's an [answer](https://github.com/vuejs/vue/issues/6567#issuecomment-328549186) from Evan You.

    > We do support all the way down to 4.4.

2. Suspect 2: [axios]

    The next thing I suspected was [axios]. To use [axios] in IE8, [you need to add a Promise polyfill](https://lovemewithoutall.github.io/it/vue-ie-support-with-es6-promise/). But that didn't fix it either.

3. Suspect 3: ECMAScript 2015 (es6)

    The next thing I suspected was ECMAScript 2015 (es6). I should have thought of this first when I saw the error message, but I had gotten so used to es6 that it took me a while to come up with it. Naturally, old versions of browsers don't support es6. If I had run babel, this wouldn't have happened. Better late than never, I ran the [BABEL repl](https://babeljs.io/en/repl.html).

## The Resolution

I ran the code converted to the old JavaScript syntax, and now it works fine. The error in the capture above was caused by using `let`. Naturally, es6 arrow functions and object destructuring don't work either, so you have to write them in the old syntax. And that's how the problem was solved.

## Miscellaneous

1. Something I learned this time: starting from Android 4.4.3 KitKat, the WebView changed significantly. In versions before 4.4 KitKat, a prototype WebView written with Chromium was used, but in Android 4.4.3 KitKat, a WebView based on Chrome 30.0.0.0 is used ([reference](https://medium.com/@pks2974/fads-9eea83f47607)).

2. Among the users of the app in question, those on KitKat and lower versions were `2.2%`. There are still quite a lot of users (?).

3. I confirmed that [Vue.js] runs fine on Android Gingerbread as well.

## The End!

[Vue.js]: https://vuejs.org/
[axios]: https://github.com/axios/axios
