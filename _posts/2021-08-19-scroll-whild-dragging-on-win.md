---
layout: single
title: Chromium behavior - wheel scroll while dragging on Windows OS
date: 2021-08-19 18:18:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/3-Tasten-Maus_Microsoft.jpg/440px-3-Tasten-Maus_Microsoft.jpg"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/22/3-Tasten-Maus_Microsoft.jpg/440px-3-Tasten-Maus_Microsoft.jpg"
categories:
- IT
tags: [chrome]
---

## HTML5 spec
From the moment that the user agent is to initiate the drag-and-drop operation, until the end of the drag-and-drop operation, device input events (e.g. mouse and keyboard events) must be suppressed.

## But... Chromium works differently depend on OS. Very confusing.
* In MacOS, Chromium allows wheel scroll while dragging that using HTML5 drag API just because safari can do it.
* In Windows OS, Chromium does not allow wheel scroll while that using HTML5 drag API because it is HTML spec(https://html.spec.whatwg.org/multipage/dnd.html#drag-and-drop-processing-model) very justified.

Some web apps seem to be allow to wheel scroll while dragging. It makes confuse for many developers and users. These web apps are not using HTML5 drag API. They are using custom drag implementation like jQuery UI & react-beautiful-dnd. On the contrary, other libraries like react-dnd does not support wheel scroll while dragging in Windows OS, because they are using pure HTML5 drag API.

### Reference
* Jquery: https://codepen.io/GreenSock/pen/YPvdYv
* HTML5 spec: https://html.spec.whatwg.org/multipage/dnd.html#drag-and-drop-processing-model
* HTML5 spec discuss: https://github.com/whatwg/html/issues/4794
* Chromium issue
  * https://bugs.chromium.org/p/chromium/issues/detail?id=642214
  * https://bugs.chromium.org/p/chromium/issues/detail?id=556169

20210723
