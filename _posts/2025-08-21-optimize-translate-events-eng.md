---
layout: single
title: Optimizing Transition/Translate Event Handlers
date: 2025-08-21 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://i.namu.wiki/i/791QGb45uFOV2j8KzEp08FrlW4Iu0ovdNWzDEKXCdyJmse7DQmJQLrOVKGXf_Ahyr0VYOAcCsEJS05UPHcPMPXGQCE4sYPo-kTfStXXqSUq1VnP0hrq9RvBVn4xAi9rI1RhbjwSylxJhjmOWwXnTMQ.webp"
    image: "https://i.namu.wiki/i/791QGb45uFOV2j8KzEp08FrlW4Iu0ovdNWzDEKXCdyJmse7DQmJQLrOVKGXf_Ahyr0VYOAcCsEJS05UPHcPMPXGQCE4sYPo-kTfStXXqSUq1VnP0hrq9RvBVn4xAi9rI1RhbjwSylxJhjmOWwXnTMQ.webp"
categories:
- IT
tags: [Javascript]
---

While developing with [SwiperJS](https://swiperjs.com/), the Transition/Translate event handlers caused performance issues, so I put together a quick summary.

## One-line summary

In event callbacks that move the DOM, use requestAnimationFrame.

## Three-line summary

-	Transition-related events such as swipe/scroll/pointer movement and a library's translate callback fire more often than the frame sync (requestAnimationFrame). When that happens, even on a device that isn't low-end (a cheap phone), frames stutter and the callback's computations get dropped.
- Don't compute or write to the DOM directly inside the event callback. Instead, use requestAnimationFrame so the function runs only once, right before paint. This is throttling.
- If there's a value computed for use inside the requestAnimationFrame callback, compute it inside the requestAnimationFrame callback. Otherwise the computation lags behind and produces the wrong result.



## The symptom: events are "noisier" than "frames"

A swipe/pointer movement or a library's translate callback (e.g., Swiper's onSetTranslate) can fire more often, regardless of the frame rate... or so they say, but as of 2025, in webviews you get dropped events 100% of the time.

- What does it mean for an event to be "dropped"? An event that should have run never fires.
- Why doesn't the event run? Because if you call a function too many times (too often), the browser can't finish executing all the called functions before rendering completes.
- So what should you do? Reduce the number of calls.
- How much should you reduce it? Just enough that the screen doesn't move oddly.
- How much is "enough that the screen doesn't move oddly"? Once per frame of on-screen movement is enough.
- On the other hand, if events come in N times per frame, the callback touches the DOM every time, causing redundant computation and unnecessary style/composite work.

There's another trap: the state right after an event is often an intermediate value. For example, translate sometimes momentarily produces a "spiking" value, and if you fail to handle this "spiking" value properly, the screen can move oddly, stop partway, move while stuttering, or not move at all.

Before discussing the solution, here's a very brief summary of the browser's rendering pipeline. Now that the Chrome team has released RenderingNG, even this is gradually starting to become inaccurate...

```
JavaScript -> Style/Layout -> (composite if needed) -> Paint
```

## The solution: compute inside requestAnimationFrame and run "only once per frame"

1.	Throttling effect: requestAnimationFrame is called right before style recalculation. And it runs only once per rendering cycle. requestAnimationFrame does the throttling for you.
2.	Consistent snapshot: inside the requestAnimationFrame callback, re-read the latest state (e.g., swiper.translate, swiper.height) and use those values to perform both the check and the computation at the same time. Since it's right before the style calculation of the same frame, you're less swayed by intermediate values, and you prevent dirty closures like "the check uses the latest value but the write uses the old value."

## Bad example vs. good example

### Bad example: computing/writing directly in the event callback

```javascript
function onSetTranslate(swiper: Swiper, translate: number) { // events come in more often than frames
  if (swiper.translate % swiper.height === 0) return; // gets caught by an intermediate value and skips unnecessarily

  const y = getTranslateY(swiper, translate); // check/compute timing mismatch

  videoDiv.style.transform = `translate3d(0,${y}px,0)`; // pointlessly writes to the DOM multiple times per frame
}
```

### Good example: in the event callback, only schedule the rAF; do the actual work inside the rAF

```javascript
function onSetTranslate(swiper: Swiper) {
  requestAnimationFrame(() => {
    // "re-read" inside the rAF
    const translate = swiper.translate ?? 0;
    const height = swiper.height || 0;

    // perform the check and computation with the same snapshot value
    const y = Math.max(translate, translate % height)        
    videoDiv.style.transform = `translate3d(0,${y}px,0)`;
  });
}
```

### Etc.

-	Events like a transition cancel/reset branch (transitionstart/transitionend) only happen once, so there's no need to use requestAnimationFrame.
- But always handle movement (translate) synchronization in a rAF loop.
- As an aside, I'd advise against building a TikTok-style UX frontend with something like SwiperJS. Just build it from scratch yourself. <b>SwiperJS is not a library meant for building TikTok.</b> I want to cry.

## Conclusion

For movement events, read, compute, and write inside requestAnimationFrame.

EOD

20250821
