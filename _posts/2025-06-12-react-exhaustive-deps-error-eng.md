---
layout: single
title: The Technical Background Behind the `exhaustive-deps` Warning That Only Appears in React Custom Hooks
date: 2025-06-12 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/React-icon.svg/200px-React-icon.svg.png"
categories:
- IT
tags: [react]
---

### Diagnosing dependencies for `useRef` and `useEffect`

## The technical background behind the `exhaustive-deps` warning that only appears in custom hooks

#### Stating the problem

There are two scenarios that use the same `ref` object.

1. Calling `useRef()` directly **inside a component**
2. A **custom hook** returning the result of `useRef()`

The runtime behavior is identical in both cases, yet the ESLint rule `react-hooks/exhaustive-deps` only raises the warning "add ref to the dependency array" for the second pattern. Why is that?

---

## 1. React runtime: the **stable identity** of the `useRef` object

`useRef()` creates the `{ current }` object exactly once on the initial render, and **reuses the same object reference** on every subsequent render. The official React documentation explicitly guarantees this. ([stackoverflow.com][1])

```tsx
function Comp() {
  const boxRef = useRef<HTMLDivElement>(null); // always the same object
  /* … */
}
```

> **Result**
>
> * Whether the dependency array is `[]` or `[boxRef]`, the object reference (identity) does not change, so `useEffect` does *not run again*.
> * In development Strict Mode, the initial render intentionally happens twice, so the mount and unmount effects are invoked both times.

---

## 2. The `exhaustive-deps` rule: the **limits of static analysis**

`eslint-plugin-react-hooks` scans the source code at compile time, and internally maintains a *"Stable Return Value whitelist."*

```text
SAFE_RETURNS = { useRef, useState, useReducer, … }
```

* When a hook called *directly* **inside a component** is included in this list, the corresponding variable is *automatically considered stable*, and omitting it from the dependency array is allowed.
* A **custom hook** is a *black box* whose function body cannot be inspected. Since the static analysis tool cannot rule out the possibility that the hook creates a different object every time, it takes a *conservative* approach and emits the warning. This design principle can also be confirmed in the GitHub issues. ([github.com][2], [github.com][3])

In other words, the root of the problem lies in the information asymmetry between the **runtime guarantee** and the **static analysis hints**.

---

## 3. Runtime comparison: `[]` vs `[ref]`

| Dependency array | Re-run condition | Execution count in production\* |
| ------- | ------------------ | --------------- |
| `[]`    | No condition       | 1 time          |
| `[ref]` | Only when the `ref` object reference changes | Object is immutable ⇒ 1 time |

\* In React Strict Mode (development environment), an extra +1 happens intentionally

Since the `ref.current` value is mutable, putting it in the dependencies does **not detect** changes. To track value changes, you need to use a separate pattern such as `useState`, `useSyncExternalStore`, or a callback ref.

---

## 4. Practical strategies

| Approach | Implementation example | Benefits / Considerations |
| -------------------------- | --------------------------------------------------------- | ----------------------------------------- |
| **Add ref to the array** | `useEffect(fn, [myRef]);` | The ref identity is immutable → runs once, warning resolved |
| **Move the effect inside the hook** | Run `useEffect` inside `useFocusRef()` | Cleaner component code, removes the warning entirely |
| **ESLint ignore comment** | `// eslint-disable-next-line react-hooks/exhaustive-deps` | Last resort. Carries maintenance risk |
| **Register the custom hook as a 'stable hook' (fork)** | Use a forked `exhaustive-deps` plugin | Not supported by the official rule; worth considering depending on team size ([reddit.com][4]) |

---

## 5. Conclusion

1. The **React runtime** guarantees the *identity immutability* of the object returned by `useRef`.
2. The **`exhaustive-deps` rule** treats only built-in hooks as stable values, and assumes custom hooks are potentially changing values.
3. As a result, the execution count is the same whether you pass `[ref]` or leave it as `[]`, but the *static analysis warning* is only exposed for custom hooks.
4. To remove the warning, the safest and most maintenance-friendly approaches are to **add the ref to the array** or to **move the effect inside the hook**.

> Once you understand the difference in perspective between the runtime guarantee and static analysis, there is no longer any reason to be confused about "why only I see the warning." You can resolve it cleanly by adjusting the dependency array to match your project's rules or by restructuring the hook design. Happy Hooking! 🪝

---

[1]: https://stackoverflow.com/questions/72402329/what-is-the-exact-behaviour-of-useref-react-hook-does-the-object-get-recreated "What is the exact behaviour of useRef react hook? Does the object ..."
[2]: https://github.com/facebook/react/issues/22539 "Allow custom hooks to return stable results · Issue #22539 - GitHub"
[3]: https://github.com/facebook/react/issues/24112 "Suggestion: [eslint-plugin-react-hooks] - exhaustive-deps ... - GitHub"
[4]: https://www.reddit.com/r/reactjs/comments/1d5rkro/can_eslint_reacthooksexhaustivedeps_safely_be/ "Can ESLint \"react-hooks/exhaustive-deps\" safely be ignored with ..."

EOD

20250612
