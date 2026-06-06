---
layout: single
title: How to check whether the platform running a browser or webview is an iPad
date: 2025-02-06 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://1000logos.net/wp-content/uploads/2017/08/Chrome-Logo-768x432.png"
    image: "https://1000logos.net/wp-content/uploads/2017/08/Chrome-Logo-768x432.png"
categories:
- IT
tags: [iOS]
---

## The Problem

When you want to know whether the platform running a webview or web browser is an iPad, you usually use the code below. GPT also suggests this.

```typescript
const isIpad = navigator.maxTouchPoints > 1 && /MacIntel/.test(navigator.platform);
```

<b>However, the code above does not work in the `Chrome browser`.</b> That is because `navigator.platform` in the `Chrome browser` is `iPad`.

On top of that, navigator.platform is a deprecated API.

### The Solution

You can create a function like the one below to check whether the device is an iPad. With this approach, it works fine in the `Chrome` browser as well.

```typescript
const isIPadOS = () => {
  const haveTouchPoint =
    navigator.maxTouchPoints && navigator.maxTouchPoints > 1;

  const isiPadUserAgent =
    /Macintosh/i.test(navigator.userAgent) || /iPad/i.test(navigator.userAgent);

  return haveTouchPoint && isiPadUserAgent;
};
```

20250206
