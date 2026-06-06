---
layout: single
title: HTML video tag onTimeUpdate firing interval
date: 2022-05-16 12:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2022-05-16-html5-video-tag-ontimeupdate-interval.png"
    image: "/assets/images/2022-05-16-html5-video-tag-ontimeupdate-interval.png"
categories:
- IT
tags: [HTML5]
---

The callback attached to the onTimeUpdate property of the HTML video tag fires, at its slowest, once every 250ms.

In my tests, when I just let the video play normally, the onTimeUpdate callback was called about 4 times per second.

Here is the relevant part of the HTML5 spec document.

![html5 spec](/assets/images/2022-05-16-html5-video-tag-ontimeupdate-interval.png)

### References

- https://html.spec.whatwg.org/#event-media-timeupdate
- https://stackoverflow.com/a/12325960/8403199

20220413
