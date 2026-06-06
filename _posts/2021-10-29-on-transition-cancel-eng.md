---
layout: single
title: on transition cancel
date: 2021-10-29 18:33:30.000000000 +09:00
type: post
header:
    teaser: "https://image.shutterstock.com/z/stock-vector-cancel-stamp-222272467.jpg"
    image: "https://image.shutterstock.com/z/stock-vector-cancel-stamp-222272467.jpg"
categories:
- IT
tags: [javascript]
---

What happens when a transition in progress is cancelled? The transitionEnd event doesn't fire. When this happens, it causes errors in JavaScript apps that trigger events based on transitionEnd.

Let's look at an example.
There's a carousel that shows the next element according to a timer. The carousel uses a transition to advance through elements. And when the transition finishes, it fires a transitionEnd event to advance to the next element again.
But what if CSS display: none is applied in the middle of the transition to the next element? The transitionEnd event won't fire. The event that advances to the next element never fires, and the carousel dies.

Is there no solution?
There's the transitionCancel event (https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/transitioncancel_event). Let's use this!

```typescript
// react
const wrapperRef = useRef<HTMLDivElement>(null);
const div = wrapperRef.current;

if (!div) return;

const restartTransition = () => {
    initForNextCarouselTransition();
    startTransition();
  }

div.ontransitioncancel = restartTransition;
```

It's not yet available in React synthetic events...

According to the TypeScript documentation, ontransitioncancel is described like this.
The ontransitioncancel property of the GlobalEventHandlers mixin is an event handler  that processes transitioncancel  events.
The transitioncancel event is sent when a CSS transition  is cancelled. The transition is cancelled when:
- The value of the transition-property property that applies to the target is changed
- The display property is set to "none".
- The transition is stopped before it has run to completion, e.g. by moving the mouse off a hover-transitioning element.
Supported by:
Chrome 87, Chrome Android 87, Edge 87, Firefox 53, Opera 73, Safari 13.1, Safari iOS 13.4

If you need to support older browser versions, use IntersectionObserver instead.


```typescript
// useOnScreen.ts code
import {RefObject, useEffect, useState} from "react";

type Props = {
  ref: RefObject<HTMLElement>;
}

type useOnScreen = (props: Props) => boolean

const useOnScreen: useOnScreen = ({ref}) => {
  const [isIntersecting, setIntersecting] = useState(false)

  const observer = new IntersectionObserver(
    ([entry]) => setIntersecting(entry.isIntersecting)
  )

  useEffect(() => {
    if (!ref.current) return;
    observer.observe(ref.current)

    return () => { observer.disconnect() }
  }, [])

  return isIntersecting
}

export default useOnScreen;
```


```typescript
// tsx code
const wrapperRef = useRef<HTMLDivElement>(null);
const isVisible = useOnScreen({ref: wrapperRef})

useEffect(() => {
  if (isVisible) {
    startTransition();
  } else {
    initForNextCarouselTransition();
  }
}, [isVisible])
```

20210924
