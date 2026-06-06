---
layout: single
title: "React Router ScrollRestoration Debugging Log — Why Wouldn't the Scroll Move?"
date: 2025-04-25 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://reactrouter.com/splash/hero-3d-logo.webp"
    image: "https://reactrouter.com/splash/hero-3d-logo.webp"
categories:
- IT
tags: [react-router]
---

# React Router ScrollRestoration Debugging Log — Why Wouldn't the Scroll Move?

In a Single-Page Application (hereafter SPA), restoring the scroll to the position the user was reading when they navigate back and forward is crucial to UX quality. React Router v6.4+ provides the <ScrollRestoration /> component for exactly this purpose, but contrary to the official claim that "you just have to drop it in," it may not work at all due to CSS and layout constraints.

This post is a debugging note that tracks down why <ScrollRestoration /> wouldn't budge an inch, all the way through to working around it with Zustand + scrollIntoView. In particular, it covers in detail how the `height: 100vh` + `overflow:hidden` set on `<html>`/`<body>` ended up blocking the root scroll itself, and the process of actually proving that the overflow setting was the "culprit."

⸻

```
Table of Contents
	1.	Describing the Problem
	2.	Adding a Single <ScrollRestoration /> Line → Failure
	3.	The Catastrophe Caused by "Root Scroll Lock"
	4.	Is overflow Really the Culprit? (Experiment & Proof)
	5.	Dissecting the Internal Behavior of React Router v7 <ScrollRestoration />
	6.	Comparing Solution Strategies — Unlock the Root Scroll vs. Work Around It
	7.	The Workaround: Zustand + data-id + scrollIntoView
	8.	Checklist & Practical Tips
	9.	Retrospective — How to Avoid the Same Pitfall?
```
⸻

## 1. Describing the Problem

###		Structure
	•	/qna : an infinite-scroll Q&A list
	•	/answer/:id, /complaint/:id : detail views
	•	Requirement : when going detail → back/forward, restore the previous scroll position in the list

### Global CSS

```css
html, body {
  height: 100vh;    /* (1) Make the document height exactly equal to the viewport */
  overflow: hidden; /* (2) Completely remove the root scrollbar               */
}

.c-qna {
  height: 100%;
  overflow: auto;   /* The actual scrolling is handled by this div */
}
```

`height: 100vh` had to be kept because of a mobile WebView issue, and `overflow:hidden` because of a parallax effect — both were non-negotiable conditions.

If you're a CSS expert, you can probably already guess the root cause just from looking at this.

⸻

## 2. Adding a Single ScrollRestoration Line → Failure

Contrary to React Router's bold promise that you just need to add a single line next to `<Outlet />`, the scroll didn't work at all.

```javascript
function AppLayout() {
  return (
    <>
      <ScrollRestoration storageKey="home" />
      <Outlet />
    </>
  );
}
```

	•	Expected : automatic restoration on back/forward
	•	Reality : scroll was always 0 px, with no warnings or errors → debugging begins

### Checking the browser DevTools > Performance panel

	•	Clicked the back button, but
	•	no scroll movement occurs at all!


⸻

## 3. The Catastrophe Caused by "Root Scroll Lock"

### Observation	Result

Ran `window.scrollTo(0, 400)`, but the scroll was still at 0px.

```javascript
const root = document.scrollingElement;  // <html>
console.log(root.scrollHeight, root.clientHeight); // identical
window.scrollTo(0, 300);                 // no effect
```

On the other hand, the inner container's `scrollTop` did change and an actual scrollbar was shown → confirmed that the real scrolling element is the div.

Because the browser determines that the root "cannot move," all root-targeting APIs (`window.scrollTo`, `ScrollRestoration`) are rendered completely ineffective.

⸻

## 4. Is overflow Really the Culprit?

### 4-1 Experiment ① — Removing overflow:hidden

```css
html, body {
  height: 100vh;
  /* overflow: hidden; ⬅︎ commented out */
}
```

	•	`window.scrollTo` worked correctly right away
	•	`<ScrollRestoration />` also restored successfully immediately

### 4-2 Experiment ② — Keeping overflow, removing only height:100vh

```css
html, body {
  /* height: 100vh; ⬅︎ commented out */
  overflow: hidden;
}
```

	•	Scrolling is still impossible (if the root has overflow hidden, scrolling is blocked regardless of the inner height)

### 4-3 Conclusion
	•	`overflow:hidden` is the main culprit behind blocking the root scroll
	•	`height:100vh` is a contributing factor that makes the `scrollHeight` calculation 1 : 1
	•	When both properties are present at the same time, "root scroll 0px" is guaranteed → ScrollRestoration is completely neutralized

The same phenomenon has been reported in many places, such as the [StackOverflow](https://stackoverflow.com/questions/66098013/window-scrollto-doesnt-work-with-overflow-and-100vh-height) and [jQuery](https://github.com/jquery/jquery/issues/4581) issues. ￼ ￼

⸻

## 5. Dissecting the Internal Behavior of React Router v7 ScrollRestoration
1.	Assign a Key to each route
1.	Store the current scroll position of each Route Key in session storage
1.	On a POP navigation (back/forward), inspect session storage and, if a scroll position exists, call the `window.scrollTo` function to restore the scroll

#### Ultimately, if the root element isn't a scrollable structure, it's a design that simply cannot work.

⸻

## 6. Comparing Solution Strategies

- Switch to a root-scroll structure & keep `<ScrollRestoration />` as is: a major overhaul of the legacy CSS and WebView is not feasible...
- Fork React Router's `<ScrollRestoration />` → extend it to scroll a child element: hard to maintain because of React Router's frequent version changes...
- Zustand + scrollIntoView: no layout changes needed & simple to implement

I chose the simplest option, Zustand + scrollIntoView.

⸻

## 7. The Workaround: Zustand + data-id + scrollIntoView

1. Create a Zustand store
1. Whenever a list item is clicked, save the item's id to Zustand
3. Attach a data-id to the list item's HTML element
4. In a useEffect, use document.querySelector to select the element corresponding to that id from Zustand, then restore the scroll with scrollIntoView

```typescript
const navType  = useNavigationType();      
const targetId = useScrollStore(s => s.targetId);

useEffect(() => {
  if (navType !== 'POP' || !targetId) return; // Restore scroll only on back/forward navigation
  requestAnimationFrame(() => {
    document
      .querySelector<HTMLElement>(`[data-id="${targetId}"]`)
      ?.scrollIntoView({ behavior: 'instant' });
  });
}, [navType, targetId]);
```

⸻

## 8. Checklist & Practical Tips
	1.	Check the CSS first — html/body overflow, height
	2.	Test a manual call to window.scrollTo
	3.	Distinguish POP vs PUSH (useNavigationType)
	4.	Secure the timing of DOM completion (requestAnimationFrame, Observer)
	5.	Prevent session key / store key collisions

⸻

## 9. Retrospective — How to Avoid the Same Pitfall?
	•	Make checking whether Root vs Inner scroll is separated your top priority.
	•	`<ScrollRestoration />` is (as of 2025-04) a root-only component. If the structure doesn't match, it's faster to boldly work around it or use a different library.
	•	The global store + scrollIntoView approach is easy to implement and fits legacy layouts well.

## One-Line Summary
"If window.scrollTo can't move, then <ScrollRestoration /> can't move either."
When you run into a scroll bug, first check whether the root is locked by CSS!

EOD

20250425
