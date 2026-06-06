---
layout: single
title: "On How Next.js Created a Closure and Stored State as the Component's Initial Value (stale closure)"
date: 2025-07-03 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js, React]
---

# Environment

- Next.js 14

# Symptom

In the Footer component of a React/Next.js-based page, I used Next.js's `Script` component to load an external script, and then implemented the logic so that the `sendLog` function would be called inside the `Script` component's `onReady` callback every time the page navigated. In the normal page flow, the `sendLog` function correctly sent state such as the component's internal `src`. The problem, however, was when it was called inside the Script component's `onReady` callback (e.g., `onReady(() => sendLog())`). In that case, `sendLog` only sent the state from the time of the initial render (i.e., the fallback values). After debugging, I found that the `onReady` callback runs a function that was registered once when the Script first loaded, and at that point the function was running while having **captured the initial state in a closure**.

In other words, the `sendLog` function defined during the render process of the React function component had stored the state values that existed at the time of the first mount as a closure. To explain in more detail: even though the state was later updated and the component was re-rendered, the `sendLog` that ran when the Script's `onReady` event was triggered operated while **referencing only the values from the initial render**. This was due to JavaScript's closure mechanism. A **closure** refers to "a form that remembers a function together with the lexical environment (scope) at the time the function was defined." Put simply, the state at the time `sendLog` was defined was stored in the function's internal memory, and when the function was later called, it only saw these stored values.

The core of this problem was the **"stale closure"** issue that arises from the combination of React's functional rendering and JavaScript closures. In a function component, a new function and environment are created on every render, and those functions capture values according to JavaScript's closure rules. As a result, when a callback function that runs later is referencing a function created before the re-render caused by a state change, the function fails to reflect the latest state and ends up using past state values.

Now let's follow the debugging process, examine how React hooks and closures work, and finally solve the problem with a `useRef`-based pattern.

```tsx
// The Footer component where the error occurred
'use client';

import Script from 'next/script';
// ...

// Bottom toolbar component
const Footer = () => {
  const [src, setSrc] = useState('');

  const sendLog = () => {
    // Send src to an external server
    console.log('sendLog', src);
    // ...
  };

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderTabbar({ onBeforeNavigate: sendLog })}
    ></Script>
  );
};

export default Footer;

```

## React Function Components and Closures

A React function component re-executes its entire rendering logic every time it is called. During this process, all the functions and variables declared inside the component are each bound to that render's **lexical context (execution context)**. Therefore, a function defined inside a function component also operates as a closure that has captured the `props` and `state` values from that rendering point. Dan Abramov explained this as "function components *capture* the rendered values." For example, in the following example:

```jsx
// react
function MessageThread() {
  const [message, setMessage] = useState('');
  const showMessage = () => {
    alert('You said: ' + message);
  };
  // ...
}
```

`showMessage` closes over the `message` value at the time of rendering. That is, each time the component renders, a new `showMessage` function is created, and the previous render's `showMessage` remembers the `message` value from that time. (This is different from class components, where `this.state` is always read as the latest value... or so Dan says, but I've never used class components, so I wouldn't know.) In an actual example, when you change the value in the message input field and press the button, the `message` inside the click handler uses the value from "the render in which the button was pressed." Dan Abramov explained that "this function component's `message` holds the `message` from the very render that returned the click handler the browser called."

In this way, the functions of a function component reference the **state values that existed at the time of their definition**. Even if the state is later updated and the component is re-rendered, the functions created in the previous render still reference the old values they captured (closed over). And if you call a function from a previous render via an external event or an asynchronous callback, this function only sees the values captured at that time. This mechanism is the heart of a \*\*closure\*\*. While this phenomenon was perfectly obvious when using only JS, it's not easy to keep in mind when developing React function components. Because a function component re-creates its functions through re-rendering every time a state value changes, it's rare to experience a situation where an internal function of the component operates as a closure and references old state—even though the function component itself is one giant closure.

Let's get back to debugging. `sendLog`, too, was a function created at the component's first rendering point, so it held the state values such as `src` at that time as a closure. Even if the state changes at the next rendering point, the closure environment stored inside `sendLog` does not change. So when you register `sendLog()` inside the Script's `onReady` callback, that function ends up sending logs based on the state values captured at the first render (which are no longer updated).

# Debugging

## Verifying the Closure

The process of tracking down a problem usually starts with **logging** and **execution flow analysis**. First, when I added a console log at the start of the `sendLog` function, the `src` value only showed up as the initial value (fallback) when `onReady` ran. On the other hand, when this function was called normally, `src` showed the latest video source URL. In other words, even though it was the same function, the result of the state update was not reflected on the `onReady` side.

Next, I checked when the `onReady` callback runs. In Next.js's `next/script` component, the `onReady` attribute runs **right after the script loads, and every time the component mounts**. In our case, when the Footer component first mounts, the `onReady` callback runs at the point the script finishes loading. At that time, `sendLog` is registered on the `onBeforeNavigate` property. The problem is that even when video-related state (for example, `src` changes) is later updated within the page, the `onReady` callback still uses the same function created at the initial mount. In other words, the state at the time `sendLog` was defined remains fixed, and the values that changed in later renders are not reflected in the state values inside the closure that gets called.

To understand this behavior, you need to grasp React's render cycle. Every time the state changes, React re-executes that component and creates new functions/variables. However, event handlers or callbacks already attached to the DOM still reference the function created in the previous render. In the example above as well, because the `onReady` callback that references `sendLog` was registered at the mount point, that callback does not change regardless of subsequent state changes and is still a **closure that captured the initial state**.

## JavaScript Lexical Scope and Closures

Let's unpack the above in terms of more general JavaScript concepts. In JS, a **closure** is the bundling of a function with the environment (lexical environment) in which the function was defined. For example:

```js
function outer() {
  let count = 0;
  function inner() {
    console.log(count);
  }
  count++;
  return inner;
}
const fn = outer();
fn(); // 1
```

In the code above, the `inner` function closes over `outer`'s `count` variable, so when `inner` is called, it remembers and prints the `count` value. It's the same inside a React component. A function defined inside a function component can access that render's local variables (state, props, etc.), and it remembers those values even after the render ends. That is, if `src = ""` in the first render, the function created in that render remembers the state where `src` is "". Even if the state is updated, the old closure still keeps the value from before the update.

Therefore, in the debugging process, the answer to "why does `sendLog` always send the same (old) value" lay in the closure's **lexical capture**. It's a structure in which the scope variables at the moment the function was defined are preserved inside the function and can be referenced later. Closures are convenient, but when they meet React, complexity arises because you have to consider the "expiration (staleness) of state according to its lifecycle." Especially when combined with asynchronous operations or events, there are cases where you end up seeing the **value the function first captured** rather than the **latest value at the intended point in time**.

## The Relationship Between React State and the Component Lifecycle

A React function component manages state and lifecycle with hooks. The state declared with `useState` has its value determined by the rendering point. For example, with `const [src, setSrc] = useState("")`, in the first render `src` is "", and after the state changes and it re-renders, the `src` value at that point is visible inside the function. However, **a closure does not automatically reflect the result of these state changes**.

As Dan Abramov pointed out, in a function component the function itself always looks at the state and props of "the very render in which that function was created." For example, in the message example, when you press the "Send" button, `showMessage` shows the `message` at that moment. The content entered afterward cannot be seen by this existing function. This is the typical pattern in which a "stale closure" occurs.

To understand closures, if you look at the component lifecycle, the component has an initial render at mount time and re-renders on every state update. If there were asynchronous logic in place like the following:

```jsx
function Footer() {
  const [src, setSrc] = useState('');

  const sendLog = () => {
    // Send src to an external server
    console.log('sendLog', lapCount, src);
    // ...
  };

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderTabbar({ onBeforeNavigate: sendLog })} // suspicious that the state shows up as the initial value
    />
  );
}
```

Here, `sendLog` captures `src=''` from the first render. When the script loads, `sendLog()` is registered on the `onBeforeNavigate` property. But the `src` that this function sees inside is the value **at the time of the first render**. Even if you later change the state with `setSrc`, that change is not reflected in the `sendLog` inside the already-registered `onReady` callback.

# Solution

## Securing the Latest State via `useRef`

To solve this problem, we need a way to read the latest state value at any time. It also needs to be a method that not only doesn't trigger a re-render but also keeps the value persistent even if a re-render happens. In such cases, you can use `useRef`. The `ref` value that useRef returns is "an object that exists as a single instance (= a reference-type value)," and what the closure (or external library) remembers is the address of that object. Unlike an immutable value, an object reference is "shallowly" copied, so you can keep swapping in the latest value into `current` (ref.current), which is a property of the ref. **The function will keep referencing the ref from the initial rendering point within the closure, but the ref.current that the function references can be stored/read at the point you need it.** This way, the sendLog function uses the latest value as intended.

This is the stale closure avoidance pattern.

For example, if you add `srcRef` to the `Footer` example above:

```jsx
function Footer() {
  const [src, setSrc] = useState('');
  const srcRef = useRef(src);

  // Update the ref when the state changes
  useEffect(() => {
    srcRef.current = src;
  }, [src]);

  const sendLog = () => {
    // Use ref.current: always reads the latest value
    console.log('sendLog (ref)', srcRef.current);
    // ...
    // The actual sending logic also uses srcRef.current
  };

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderTabbar({ onBeforeNavigate: sendLog })}
    />
  );
}
```

This way, **even inside the closure**, `src.current` always points to the most recent `src`. Because only the value is stored in the ref, the function can "escape" the state environment it originally captured and reference the current value. No matter at which render point `sendLog` runs, what `sendLog` references is `srcRef.current`, so it can read the updated state as is.

The important point in this pattern is that **the ref maintains the same object throughout the entire component lifecycle**. As in the example above, if you detect state changes with `useEffect` and update the ref, then afterward `ref.current` always remains the "most recent value." Dmitri Pavlutin also emphasized that "because the ref always evaluates the latest value (ref.current), you can avoid the `stale closure` problem." Thanks to this, the value doesn't go stale even when referenced from an external callback (non-stale & freshest).

By applying the **stale closure avoidance pattern**, you can also **put the function itself into a ref and call it.** If you put the sendLog function into the ref on every re-render using `useEffect`, the sendLog function that ref.current references is always the function from the latest render. Therefore, the state values it holds as a closure are always the latest values. Since you only need to put one function into the ref and keep updating it, this is **more convenient** than the method above, where you have to put all the values the function uses into the ref.

```jsx
function Footer() {
  const [src, setSrc] = useState('');

  const sendLog = () => {
    // ...
    // Use src inside the function
  }

  const sendLogRef = useRef(sendLog);

  // Always put the latest function into the ref on every re-render
  useEffect(() => {
    sendLogRef.current = sendLog;
  }, [sendLog]);

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderFooter({ onBeforeNavigate: sendLogRef.current })} // the onBeforeNavigate property references the latest sendLog function
    />
  );
}
```

## Walking Through the Code Flow Again

Let's go over how the `useRef` pattern above works. When the page loads and the Footer component mounts, the initial state is `{ src="" }`. At this point, `srcRef.current=""` is initialized. When the Script loads and `onReady` is triggered, `sendLog` is called from the `renderFooter` function. The `sendLog` function that runs at this point is the function object from the first render, but the value it reads inside is `src.current`. If the component is currently in a re-rendered state, `src.current` points to the latest value (e.g., "https://11st.co.kr"). Therefore, it contains the latest information when logging/sending.

By contrast, in the earlier code, when `sendLog` was first created, `src=""` was fixed in its internal memory. So even if the state later became "https://11st.co.kr", that function still used only "". If you use the **stale closure avoidance pattern**, the reference target (ref) is updated "regardless of when the function was created," so you can obtain the value you actually need. Likewise, if you use the **stale closure avoidance pattern** by putting the `sendLog` function itself into a ref, you can see the same effect. Dan's Overreacted blog also shows, with example code, that "reading the value stored in a ref gives the latest value," explaining that even when the input changes, `ref.current` always reflects the updated value.

## Strategies for Writing Defensive Code Against Closures

Finally, let's summarize the strategies for preventing bugs caused by closures.

* **Leveraging useRef:** As we saw above, when an external event or asynchronous logic needs to reference the latest value, using `useRef` to always store the latest state is a safe approach. It's especially useful when a value changes between renders and that change needs to be reflected in real time.
* **Managing the dependency array:** In `useEffect` or `useCallback`, you must specify accurate dependencies so that the hook re-runs every time the state changes. Only then is the changed state reflected in the new closure. Enable the lint tool's `react-hooks` to catch missing dependencies.
* **Functional state updates:** When changing state in asynchronous callbacks, timers, and so on, using a functional update (`setState(prev => new)`) computes based on the current state (the existence of `prev` in setState is a blessing). Pavlutin also explained that "writing the form `setCount(c => c+1)` in a callback solves the stale closure problem."
* **Restructuring:** Sometimes, instead of a complex asynchronous flow, changing the structure—rearranging the logic inside `useEffect`, or moving where you call an external library—so that it isn't tied to a closure can also be a solution. Of course, the best approach is to develop with a structure that doesn't create such complex situations in the first place.

By combining these strategies, you can greatly reduce stale closure bugs that occur at unexpected times. In conclusion, in React function components you must always consider **when and where the value captured by the closure is used**. When you need the **latest data** rather than the past value captured by the closure, you should explicitly keep the latest value through patterns such as `useRef` or functional updates.

## The Future?

It seems that using [useEffectEvent](https://react.dev/learn/separating-events-from-effects#declaring-an-effect-event) might also make it easy to solve the stale problem. However, as of now (2025.07.31.) it is an experimental feature.


**References:**
- [How Are Function Components Different from Classes? [Translation]](https://ideveloper2.dev/blog/2019-03-12--how-are-function-components-different-from-classes/)
- [Be Aware of Stale Closures when Using React Hooks](https://dmitripavlutin.com/react-hooks-stale-closures/#:~:text=component,actual%20value%20of%20the%20ref)
- Explanation of Next.js `<Script>` component events: [Next.js official docs](https://nextjs.org/docs/pages/guides/scripts#:~:text=the%20script%20will-,only%20load%20once,-%2C%20even%20if%20a)

EOD

20250703
