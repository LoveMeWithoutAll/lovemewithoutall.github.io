---
layout: single
title: "How to Solve the Slide Prepend Problem in Swiper.js + React"
date: 2025-06-12 00:49:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/11play.png"
    image: "/assets/images/11play.png"
categories:
- IT
tags: [react, swiper.js]
---

> **TL;DR**
> 
> In a **Swiper.js + React** project with Virtual Slides enabled,
> the way to *prepend* items while **keeping the slide the user is currently viewing** is:
>
> 1. Store the "current global slide index" in **Zustand**,
> 2. when extending the list at the front or back, update the index **within the same action**, and then
> 3. use a `useEffect` to call `slideTo()` **directly on the Swiper instance**.

Below I've put together the trial-and-error, constraints, and final code I went through along the way.

---

## 1. Defining the Problem

* **Requirements**

  * An infinite scroll (vertical) **Shorts** UI
  * Older videos are *prepended*, later videos are *appended*
  * The scroll position (the slide the user is viewing) must stay **unchanged**

* **Tech stack**

  * React 18
  * Swiper 11 (`swiper/react`) — **Virtual Slides** are required
  * State management: Zustand

* **The hard part**

  * When adding elements to the end of the list (append), the index of the Shorts (swiper slide) the user is currently viewing does not change, so there's no problem.
  * **When adding elements to the front of the list (prepend), the index of the Shorts (swiper slide) the user is currently viewing changes!**

> [Swiper official docs](https://swiperjs.com/swiper-api#virtual-slides-methods): *"When using Virtual Slides in React, you must not directly call the imperative API such as `swiper.virtual.prependSlide()`/`appendSlide()`."*
> This is because you have to keep the rendering **declarative** to avoid optimization and synchronization issues.

---

## 2. The Approaches I Tried (and That Failed)

| Approach                                       | One-line verdict                  | The point where it broke           |
| -------------------------------------------- | ------------------------------ | ------------------------------ |
| Remount Swiper by changing the `key`           | The screen is correct but it's a "full re-render" | State/event reset, **FPS drop**         |
| A global offset on `virtualIndex`              | Looks logically correct, but **Swiper's cache gets tangled** | Exception when duplicate virtualIndex + loop are used |
| `slideTo(+diff)` after `swiper.virtual.update()` | Leans heavily toward the imperative side          | A **render-timing race** still occurs        |

In the end, I needed a hybrid approach that *touches only the smallest control surface directly while preserving React's declarative flow as much as possible*.

---

## 3. Final Architecture

```
┌──────────────────────┐      set()/subscribe
│   Zustand Store      │◄────────────────────────┐
│  ├─ list             │                         │
│  └─ swiperActiveIdx  │                         │
└──────────────────────┘                         │
         ▲ prepend/append (sync index too)        │
         │                                        │
         │                useEffect               │
┌────────┴─────────────┐   (watch swiperActiveIdx)│
│   React Component    │─────────────────────┐    │
│ (ShortsSwiper & Hook)│                     │    │
└──────────────────────┘            slideTo()│    │
                                 ┌───────────▼────┴┐
                                 │ Swiper Instance │
                                 └─────────────────┘
```

### 3-1. The Core Perspective

| Element             | Declarative area                      | Imperative area          |
| ----------------- | ---------------------------------- | ---------------------- |
| **Slide data**     | `list` (React re-render)       | —                      |
| **Logical position of the current slide** | `swiperActiveIdx` (Zustand → component) | —                      |
| **Correcting the actual DOM position**  | —                                  | a one-liner `swiper.slideTo()` |

> **"State is React's job, position correction is Swiper's."**
> I clearly separated the authority over rendering decisions from the control over the viewer's position.

---

## 4. Code

### 4-1. Zustand Store

```ts
// listStore.ts
type listState = {
  list: Shorts[];
  swiperActiveIndex: number;   // global index
};
type listActions = {  
  appendlist(more: Play[]): void;     // append
  prependlist(prev: Play[]): void;    // prepend
  setSwiperActiveIndex(i: number): void;
};

const store: StateCreator<listState & listActions> = (set) => ({
  list: [],
  swiperActiveIndex: 0,

  addlist: (p) =>
    set((s) => ({
      list: [...s.list, ...p],
      swiperActiveIndex: s.swiperActiveIndex,     // keep the current index even when appending to the back
    })),
  prependlist: (p) =>
    set((s) => ({
      list: [...p, ...s.list],
      swiperActiveIndex: s.swiperActiveIndex + p.length, // shift by however much the viewed slide was pushed down
    })),
  setSwiperActiveIndex: (i) => set({ swiperActiveIndex: i }),
});
```

### 4-2. The Position-Correction Hook

```ts
// useSwiperIndexForPrepending.ts
const useSwiperIndexForPrepending = (
  swiperRef: MutableRefObject<Swiper | null>,
) => {
  const swiperActiveIndex = uselistStore((s) => s.swiperActiveIndex);

  useEffect(() => {
    const swiper = swiperRef.current;
    if (!swiper) return;

    // Ask Swiper to update the actual DOM position
    swiper.slideTo(swiperActiveIndex, 0, false);  // warp instantly to the current slide
  }, [swiperActiveIndex, swiperRef]);
};
```

#### Things to watch out for when using swiper.slideTo
1. The second parameter of the swiper.slideTo method must be set to 0 so that the slide changes instantly.
2. You must set the third parameter of the swiper.slideTo method, `runCallbacks`, to `false`. The sole purpose of this code is to keep the slide shown to the user. Therefore, it must not run any other business logic (analytics, etc.).
When you set `runCallbacks` to `false`, callback functions such as Swiper.js's `slideChange` **are not executed** even though the slide moves.
3. When you use the swiper.slideTo method, the moved component may be unmounted -> mounted again. Be careful, because the `IntersectionObserver` callback may run twice in this case. You cannot prevent the callback from running twice by storing a value in a ref to decide (because a ref is preserved only across re-renders).

### 4-3. The ShortsSwiper Component

```tsx
import { Swiper, SwiperSlide } from 'swiper/react';

const ShortsSwiper = () => {
  const list = uselistStore((s) => s.list);
  const setSwiperActiveIndex = uselistStore((s) => s.setSwiperActiveIndex);

  const swiperRef = useRef<Swiper|null>(null); // ref to hold the Swiper.js instance
  useSwiperIndexForPrepending(swiperRef);

  return (
    <Swiper
      modules={[Virtual]}
      virtual
      onSwiper={(swiper: typeof Swiper) => (swiperRef.current = swiper)} // store a reference to the Swiper.js instance in the ref
      onSlideChange={(swiper) => setSwiperActiveIndex(swiper.activeIndex)} // also change the index during a normal slide-swipe action
    >
      {list.map((shorts, i) => (
        <SwiperSlide key={shorts.id} virtualIndex={i}>
          {/* … */}
        </SwiperSlide>
      ))}
    </Swiper>
  );
};
```

---

## 5. Why Was This Approach the **Safest**?

| Requirement/Constraint        | How it's solved                                                           |
| ----------------------------- | ------------------------------------------------------------------------- |
| **Keep Virtual Slides**        | Draw only the minimum number of slides in the DOM, so performance ↗       |
| **Official guide: no prepend/append** | The actual data is managed by React                                |
| **Declarative philosophy**     | `list`, `swiperActiveIndex` -> **global state** <br> DOM correction is a "one-liner" command *only at the moment it's needed* |
| **UX**                         | The user doesn't feel any **render lag, jump, or reflow** at the moment of prepend |

---

## 6. Wrapping Up

In the React + Swiper + Virtual Slides combination, "prepending data to the front while keeping the scroll position" is an **edge case** that a single line of documentation won't solve.

* If you insist on **fully declarative**, performance/cache breaks down,
* and if you go **fully imperative**, React and your state branching get tangled.

When you split the roles the way I did here — **"state = declarative, position correction = minimal imperative"** — you can avoid the downsides of both worlds while getting the UX you need.

If I think of this case as a kind of frontend development pattern and sum it up, it goes like this:

> 1. Preserve the **global index** as state,
> 2. correct the index at the same time the data length changes, and
> 3. align the DOM with `slideTo()`.

## Miscellaneous

In [Swiper.js's example](https://codesandbox.io/p/sandbox/swiper-virtual-slides-react-28spe), why do they prepend while using Virtual slides? If the official example doesn't follow the guide in the official docs, what are developers supposed to look at when building things? T_T

EOD

20250612
