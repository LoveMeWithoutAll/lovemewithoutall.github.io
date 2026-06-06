---
layout: single
title: "When does a React ref get attached? And when does a ref callback run?"
date: 2025-08-14 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
categories:
- IT
tags: [React]
---

I ran into a bug with React's ref, so I decided to write up what I learned.

## One-line summary

Render (running the component function body) -> Commit (set ref -> useLayoutEffect) -> Browser paint (this is delegated to the browser) -> useEffect

The reason this order matters is:
```
-	During render, the ref is empty (null)
-	The ref is filled in during commit: initialization code that uses the ref must be deferred until after commit
```

----

## Summary of the React rendering process

1. Render Phase (the step that only computes)
  -	Runs the component function -> only computes the virtual tree. It has nothing to do with the browser DOM.
  -	At this point there is no real DOM. Therefore `ref.current` is null.
  -	<b> In the render function (the component body), anything that depends on the DOM (measuring styles, focusing, directly registering events, etc.) is forbidden. </b>

2. Commit Phase (the step that changes the actual DOM)
  - React reflects the result of the virtual tree onto the real screen.
  - During commit, React does its work in the following order:
    1.	Apply DOM changes: actually update the DOM, such as creating new nodes / changing attributes / deleting nodes, etc.
    2.	Attach/detach refs
    -	object ref: filled in via ref.current = domNode
    -	callback ref: refCallback(domNode) is called (when unmounted, refCallback is called with null for its arguments)
    3.	Run useLayoutEffect (executed synchronously right before paint)
    -	executed synchronously while the ref is already attached
    -	therefore it is suitable for `work that must be done before the screen is drawn`, such as measuring DOM style, adjusting scroll/focus, or registering synchronous events
3.	Browser paint: the screen is actually drawn by the browser
4.	Run useEffect (executed asynchronously): suitable for `work whose timing is less sensitive to the screen`, such as network calls, logging, and asynchronous tasks

20250814
