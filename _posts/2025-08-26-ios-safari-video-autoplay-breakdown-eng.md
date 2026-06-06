---
layout: single
title: "I tried to play an HTML Video element, but the video starts and then stops. Could it be a Safari bug? I dug into the WebKit source code"
date: 2025-08-26 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://support.apple.com/content/dam/edam/applecare/images/en_US/psp/psp_heroes/mini-hero-safari.png"
    image: "https://support.apple.com/content/dam/edam/applecare/images/en_US/psp/psp_heroes/mini-hero-safari.png"
categories:
- IT
tags: [safari]
---

## One-line summary

Not a bug.

## Environment

iOS Safari (any version)

## Symptom

In the iOS Safari browser, when you programmatically try to play an unmuted video element without a user gesture (`videoElement.play()`),

<b>the video sometimes momentarily starts playing and then gets cancelled, throwing a `NotAllowedError`.</b>

If it's going to block playback, why not block it from the very start? Why does it begin playing the video and then cancel it? Did I do something wrong? Maybe there's a way to make video autoplay work even without a user gesture?

Holding onto that hope, I analyzed the WebKit source code.

## Cause

As of WebKit 2.5, if you look at the source code of HTMLMediaElement,

https://github.com/WebKit/WebKit/blob/webkitglib/2.50/Source/WebCore/html/HTMLMediaElement.cpp#L4355

when you programmatically `play()` a video element,

even if it's currently playing, if the session fails to pass `playbackPermitted()`, it rejects the `play()` promise with a `NotAllowedError`.

## Conclusion

HTML video autoplay absolutely requires a user gesture.

Abandon your vain hopes.

EOD

20250826
