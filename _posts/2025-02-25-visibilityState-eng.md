---
layout: single
title: "Android WebView and document.visibilityState - Why Is It 'visible'?"
date: 2025-02-25 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Andrdoi]
---

While developing an Android app, you may have run into a situation where a Chrome WebView is running in the background. In particular, it can be confusing when calling document.visibilityState inside a page returns "visible" even though the WebView is not actually showing on the screen. In this post, we'll take a closer look at why this happens, the considerations that come with it, and the steps a developer can take.

## Introducing the Problem

When you load content using a WebView inside an Android app, the following situation arises.

	* Background execution: Even when the WebView exists on a screen the user is not currently viewing, or is hidden by another part of the app, the WebView keeps running.
	* Visibility API result: When JavaScript code inside the page calls document.visibilityState, it returns the value "visible" even though it is actually not visible.

This behavior can cause confusion, especially when it comes to resource optimization and managing background tasks.

## Android WebView and the Page Visibility API

### 1. The WebView's View Hierarchy

An Android app has a hierarchical structure composed of multiple Views. Even if a WebView is not actually shown on the screen,
as long as it remains within the app's View hierarchy, the browser engine treats that WebView as "active."
In other words, regardless of whether it is actually being rendered, as long as the WebView exists in memory, its state is still considered active.

### 2. The Design Purpose of the Page Visibility API

The Page Visibility API, such as document.visibilityState, was designed primarily to detect whether a browser tab is active.
In a typical web browser environment, you can distinguish between the tab the user is currently viewing and the ones they are not,
but an Android WebView is treated as a single view within the application, so no separate visibility state change event is fired.

	Key point: As long as an Android WebView remains in the app's View hierarchy, it is treated as being in the "visible" state even when it is in the background.

## Considerations for Controlling Background Behavior

If you want to prevent unnecessary resource consumption while in the background, or manage state the way you intend, you can consider the following approaches.

### 1. Explicit State Control
#### Using the onPause() and onResume() methods
By calling the WebView's lifecycle methods appropriately, you can stop unnecessary activity while in the background.
For example, calling webView.onPause() when the app moves to the background and webView.onResume() when it returns to the foreground
lets you control the activity inside the WebView.

### 2. Implementing Additional Custom Logic
#### Your own visibility check:
You can add separate logic within the app to determine whether the WebView is actually being displayed. This lets you detect when the WebView is in a state where it is not visible to the user, and adjust your background tasks accordingly.
I recommend using the browser's `sessionStorage` API for this.

## Conclusion

The reason document.visibilityState returns "visible" while an Android WebView is running in the background is that the WebView remains in the app's View hierarchy and the browser engine recognizes it as being in an active state.
Because the Page Visibility API was designed primarily for browser tabs, no separate visibility change event is fired in the case of an Android WebView.

Therefore, when you need to optimize resources or control the WebView's state within the app,
you need to manage the background behavior directly by using methods such as onPause()/onResume() or by implementing additional logic.

If you understand this principle and take appropriate measures, you'll be able to develop and optimize your app more efficiently.
I hope this helps with your development, and if you have any further questions or experiences you'd like to share, please leave a comment!

...is what ChatGPT wrote for me. It seems to write better than I do.

20250225
