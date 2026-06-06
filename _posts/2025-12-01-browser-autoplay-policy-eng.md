---
layout: single
title: The Great Chaos of Web Browser Video Autoplay Policy
date: 2025-12-01 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://webkit.org/wp-content/uploads/forever.gif"
    image: "https://webkit.org/wp-content/uploads/forever.gif"
categories:
- IT
tags: [video, safari]
---

## Web Browser Autoplay Policy

A web browser's video playback policy is extremely important from the perspective of user experience. And among the various policies, the most important one is the Autoplay policy. Through this policy, the browser forces the user or developer to choose one of two options. Either the video autoplays muted, or it doesn't play at all—one or the other. Frontend developers building video-related services generally find themselves searching high and low for a way to implement video autoplay.

So, under what conditions does a video autoplay? Generally, it comes down to the following:

1. When the play button is pressed via an explicit user gesture
2. When the video element has no audio track or has the muted property set

The policy above looks clear-cut, but it actually isn't. Let's dig into the abyss.

## Autoplay Is a Browser Policy Matter, Not an Explicit Standard

The `<video autoplay>` attribute exists in the HTML specification. However, the conditions under which autoplay actually executes (especially when there is sound) are not strictly defined by a standard. They vary according to each browser's policy.

iOS Safari has long strictly limited autoplay for the sake of battery life and user experience. For example, in iOS 10, it only allowed autoplay without a user gesture when the `<video>` element had no audio track or had the muted attribute, and it set a policy that immediately paused a muted video the moment it dynamically gained sound or became unmuted. You couldn't play two videos simultaneously, nor could a single user gesture grant unmuted autoplay permission to multiple videos. There were also rules such as autoplay only starting when the video was visible on screen and stopping when it scrolled off screen. Because these autoplay restrictions are implementation details specific to each browser, developers have no choice but to dig through browser documentation.

Apple's official blog even went so far as to recommend that "to play multiple videos one after another, don't create multiple `<video>` elements; instead, just swap the source of a single `<video>` element." The links below are among the few official documents related to video policy.

- [New `<video>` Policies for iOS](https://webkit.org/blog/6784/new-video-policies-for-ios)
- [Auto-Play Policy Changes for macOS](https://webkit.org/blog/7734/auto-play-policy-changes-for-macos)

As you'll see if you read them, the explanations in the documents above—written in 2016–2017—are not only insufficient, but as of 2025 browsers no longer even behave as explicitly stated or implied in those documents. Some policies have since become known through Apple's web development guides or the WebKit blog, but detailed changes (e.g., behavioral changes in iOS 17) are not specifically mentioned in the official release notes.

We can roughly guess the reason for this. To begin with, the autoplay policy was never defined by the HTML spec, and for people who experienced the internet of the late 1990s and early 2000s, video autoplay mostly just annoyed users. On mobile devices, there's also the matter of device performance. Downloading, decoding, and displaying video output while simultaneously responding nimbly to user gestures was, for a long time, impossible in the mobile environment.

In the end, developers have to figure out these changes by experimenting directly or by analyzing the WebKit source code. In other words, because autoplay is closer to a browser policy matter than to a standard, the only way to know what behavior is possible in the latest iOS is to peer into the code or test it on actual devices.

## The iOS 17 Change: A Single Gesture Grants Unmuted Autoplay Permission to Multiple Videos

A noticeable relaxation of the policy was discovered in **the Safari engine (WebKit) starting with iOS 17**. It became possible to obtain permission, with just a single user gesture, to autoplay every `<video>` element present in the current document with sound on.

For example, when a user touches the screen and you call play() on the video within that single event handler, in the iOS 17 Safari and WKWebView environments, every video element that had been created at the time the event handler was invoked obtains autoplay permission in the unmuted state.

In earlier iOS versions (e.g., iOS 16.4 and below), under the same circumstances only the first video would play while the rest were blocked or muted, but I confirmed that in iOS 17 this restriction has been lifted. I verified this fact by experimenting on an actual iPhone running iOS 17, testing with both mobile Safari and WKWebView. In particular, I got the same result even under WKWebView's default configuration (using the default autoplay policy without any extra settings).

This change isn't documented officially, but it carries significant meaning from a developer's standpoint. Now, when implementing a TikTok-style feed, once the user permits playback on the first video, it becomes possible to continuously autoplay the subsequent videos with sound. This means that in Safari environments on iOS 17 and above, you can implement a user experience much closer to TikTok. Of course, variables such as the user explicitly allowing/blocking autoplay for that site or low-power mode still exist. But the default policy itself has been significantly relaxed.

## User Activation

So what exactly does a "user gesture" refer to? To understand this issue, you need to be familiar with the User Activation API and the scope of a "user gesture." The HTML spec introduced the concept of user activation to prevent certain web APIs from being abused to perform actions that disturb the user (e.g., opening popups, playing notification sounds, etc.). In short, there are APIs that are only permitted when the user has made a meaningful interaction (a click, keydown, etc.). Media playback is one of them.

You can check the User Activation API via `navigator.userActivation`. On iOS, this is available starting from version 16.4.

```javascript
> navigator.userActivation

UserActivation {hasBeenActive: true, isActive: true}
```

The two properties of the `UserActivation` object mean the following:

- isActive -> transient activation
- hasBeenActive -> sticky activation

The scope of user activation is divided into two types: **transient activation** and **sticky activation**. The MDN documentation makes it easy to understand.

- [Transient activation](https://developer.mozilla.org/en-US/docs/Glossary/Transient_activation)
- [Sticky activation](https://developer.mozilla.org/en-US/docs/Glossary/Sticky_activation)


Let me explain briefly. Permission obtained through transient activation is only active temporarily. Permission obtained through sticky activation persists thereafter. Both types of permission apply to the entire browser tab (window), and they apply within the same page when the origin is identical.

[Autoplay of an HTML Media element generally has sticky activation applied to it](https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/User_activation#comparison_between_transient_and_sticky_activation:~:text=Autoplay%20of%20Media%20and%20Web%20Audio%20APIs%20(in%20particular%20for%20AudioContexts)). But Safari does not behave as the MDN documentation describes. There are many pitfalls.


### Pitfall 1. It must satisfy the condition of "having been triggered by a user gesture"

For example, code like this is naturally allowed because video.play() is called as a result of a user gesture.

```javascript
button.addEventListener('click', () => { video.play(); }) 
```

On the other hand, if you start playback from a callback that is not directly related to a user event, like the following, it is not recognized as a call triggered by a gesture.

```javascript
video.addEventListener('canplaythrough', () => { video.play(); })
```

The key is that on the browser's call stack, it must flow directly in the order of event handler -> play().

### Pitfall 2. When only "transient activation" is active in React

You might wonder why React suddenly comes up while discussing WebKit and the HTML spec. But since UI rendering libraries with architectures similar to React Fiber will continue to appear, this is something we absolutely must address. Transient activation is only active while the commands stacked on the browser's call stack from a single user gesture are executing. The problem arises when React Fiber pauses heavy work.

React's execution model, particularly the Fiber scheduler, has the characteristic of splitting rendering work into multiple pieces and pausing (yielding) as needed or resuming later. When a user clicks and you call setState in that callback, React may schedule the render and DOM update asynchronously, and in the meantime the browser's transient activation expires. As a result, the developer clearly thinks they called play() from a user gesture, but in reality play() executes after leaving the browser's call stack (e.g., in a useEffect or an async callback), and they end up seeing the activation permission denied.

To avoid this, the key principle is simple. A call that needs to obtain media playback permission must be executed within the synchronous context of the user event handler. For example, safe patterns in React are as follows:

- Call video.play() directly inside the event handler (before setState if possible).
- If you must change the render state first, force the rendering synchronously with react-dom's flushSync and then immediately call play().

#### A simple example:

```javascript
// Recommended: play directly from the event handler
function onTouch(e) {
  document.querySelector('video').play(); // play the video first
  
  setPlaying(true); // then change the state
}
```

#### Example of forcing a synchronous render:

```javascript
import { flushSync } from 'react-dom';

function onStart(e) {
  flushSync(() => setShowing(true)); // first reflect the state change synchronously
  
  videoRef.current?.play(); // then call play within the same call stack
}
```

A small pitfall: you might think you can secure transient activation by calling it from an earlier event such as pointerdown/touchstart, but this method does not guarantee that activation is active, so it's best avoided. This is especially true in low-performance mobile device environments.


### Pitfall 3. await and setTimeout

By this point it should be obvious, but code triggered by await and setTimeout runs after transient activation has already expired. Therefore, if you need transient user activation permission, you must trigger it immediately, as in the React examples above. requestAnimationFrame likewise causes transient activation to expire if you invoke the callback repeatedly.


### Pitfall 4. Consuming transient user activation

Some browser actions or web APIs (e.g., opening a popup, media.play() in some cases, fullscreen requests, etc.) require user activation, and when such an action executes, the browser may determine that it has consumed that activation. As a result, subsequent operations stemming from the same user gesture no longer benefit from the transient activation. In other words, once activation is "used up," it may not apply to the next call.

If you need video autoplay permission, you must take care not to consume that permission and handle the necessary calls within the same synchronous context. As discussed above, this policy differs by browser/version, so you must absolutely test the actual behavior per device. You may need to branch your logic by checking whether `navigator.userActivation.isActive` is true or false.


### Pitfall 5. The phenomenon where video Autoplay works briefly even without User Activation permission

In versions below iOS 17, the video briefly autoplays even though there is no User Activation or it has already been consumed. But soon a `NotAllowedError` occurs and the video stops. In versions below iOS 16.4, where the User Activation API is not supported, there's no way to verify the reason, so nothing can be done about it—but in iOS 16.4, the UserActivation API is explicitly supported, so even though the code branches based on whether activation is present, it still behaves this way.

You can verify the cause by referring to the article I wrote in the past, below.

[I tried to play an HTML Video element, but the video plays partway and then stops. Isn't this a Safari bug? I cracked open the WebKit source code](https://lovemewithoutall.github.io/it/ios-safari-video-autoplay-breakdown/)

This phenomenon occurs because WebKit first kicks off video playback as a promise (because it requires a lot of resources) and simultaneously checks whether it holds the activation flag.


## How Should You Autoplay Video on Versions Below iOS 17?

Experienced developers will already have realized that avoiding the many pitfalls listed above is nearly impossible. Video autoplay is, in effect, not a matter of sticky vs. transient user activation. It's a matter of heuristic policies defined in the browser's internal implementation.

To solve this problem, I propose a heuristic approach that separates iOS 17 and above from versions below it. The reasons are as follows.

As explained earlier, in iOS 16 and below, user activation permission is required for each media element individually. In iOS 17 and above, all media elements created in that document are granted user activation permission.

If you want to provide a TikTok-style continuous playback experience on versions below iOS 17, you must use a method that creates **only a single `<video>` element and reuses it by swapping only the `src` attribute (the Single Video Element Pattern)**. If the user pressed the play button on the first video to obtain permission, that video element enters a "blessed" state, and even if you change the source afterward, it can autoplay with sound on. On the other hand, the approach of creating a new `<video>` tag each time and adding it to the DOM requires a new user gesture for each tag, so continuous playback is impossible.


## Conclusion

A gesture is absolutely required for video autoplay. But the scope to which a gesture applies is somewhat murky. The Autoplay policy on the web is influenced more by browser vendors' policies and implementations than by standard specs. In particular, in the case of mobile Safari, the behavior differs from version to version. You must not assume "of course it'll just work." Testing on various devices and versions is necessary, and sometimes you also need to open up the WebKit source code yourself.

I wish all video-related developers the best of luck.

EOD

20251201
