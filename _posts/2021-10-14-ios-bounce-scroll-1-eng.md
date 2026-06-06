---
layout: single
title: "Safari's bounce scroll problem (1)"
date: 2021-10-14 00:46:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/5/52/Safari_browser_logo.svg"
categories:
- IT
tags: [frontend]
---

When you work with scroll events in Safari, you run into a strange behavior. It's a UI where the top of a scrollable container bounces like a rubber band, and this is called iOS native-style scrolling (source: https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/css/property/-webkit-overflow-scrolling). Let's call it bounce scroll (many people call it that).

Affected devices and browsers
- desktop safari
	- does not occur by default
	- occurs only on devices that apply inertial scrolling (e.g. macOS touchpad)
- iOS safari: occurs by default when scrolling with touch

When a scrollable container's scroll bar is
- positioned at the very top
	- if bounce scroll is active
		- scrollTop becomes less than 0.
- positioned at the very bottom
	- if bounce scroll is active
		- scrollTop becomes greater than scrollHeight - clientHeight.

The code to check whether bounce scroll is active is as follows.

```javascript
const isBounceScroll = (div) => {
    if (reverse) { // when scroll is in reverse mode (flex-direction: column-reverse)
      if (div.scrollTop > 0) return true; // overscrolled past the bottom of the scroll
      if (div.scrollTop < div.clientHeight - div.scrollHeight) return true; // overscrolled past the top of the scroll
    } else { // when in the normal scroll direction
      if (div.scrollTop < 0) return true; // overscrolled past the top of the scroll
      if (div.scrollTop > div.scrollHeight - div.clientHeight) return true; // overscrolled past the bottom of the scroll
    }
    return false;
}
```

Once you've confirmed that bounce scroll is active, you can add appropriate defensive code. For example, in an infinite scroll, when bounce scroll is active you can block the execution of the scroll event handler to fix the bug where the scroll jumps abruptly.

So, can debounce be the solution? If you use debounce, the side effect can still happen after the delay specified by the timeout. So let's take a closer look.

Test environment
- iOS 14.6
- iPhone 12

20210804
