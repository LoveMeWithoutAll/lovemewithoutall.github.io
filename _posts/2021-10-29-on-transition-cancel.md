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

진행 중인 transition이 취소되면? transitionEnd event가 발생하지 않는다. 이렇게 되면 transitionEnd를 기준으로 event를 발생시키는 javascript 앱에 오류가 발생한다. 

예를 들어보자.
timer에 맞춰 다음 element를 보여주는 carousel이 있다. carousel은 transition을 사용하여 element를 돌린다. 그리고 transition이 끝나면 transitionEnd event를 발생시켜 다음 element를 또 돌린다.
하지만 다음 element로 transition 도중에 CSS display: none이 적용된다면? transitionEnd event는 발생하지 않는다. 다음 element를 돌리는 event는 영원히 발생하지 않고, carousel은 죽어버린다.

해결 방법은 없는가?
transitionCancel event(https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/transitioncancel_event)가 있다. 이걸 사용하자!

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

react synthetic event에는 아직 없다...

typescript 문서에 따르면  ontransitioncancel은 이렇다.
The ontransitioncancel property of the GlobalEventHandlers mixin is an event handler  that processes transitioncancel  events.
The transitioncancel event is sent when a CSS transition  is cancelled. The transition is cancelled when:
- The value of the transition-property property that applies to the target is changed
- The display property is set to "none".
- The transition is stopped before it has run to completion, e.g. by moving the mouse off a hover-transitioning element.
Supported by:
Chrome 87, Chrome Android 87, Edge 87, Firefox 53, Opera 73, Safari 13.1, Safari iOS 13.4

만약 하위 버전 브라우저를 지원해야한다면 IntersectionObserver를 사용하자


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
