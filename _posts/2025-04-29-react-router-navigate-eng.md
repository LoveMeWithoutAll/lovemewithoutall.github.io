---
layout: single
title: "A Complete Analysis of React Router's useNavigate Hook - \"It's just one navigate('..'), so why does it jump up two levels?\""
date: 2025-04-29 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://reactrouter.com/splash/hero-3d-logo.webp"
    image: "https://reactrouter.com/splash/hero-3d-logo.webp"
categories:
- IT
tags: [react-router]
---

# Analyzing How react-router's useNavigate Hook Works - Where Does navigate Start From?

## Environment
- react-router v7.5

## One-line Summary

Where `navigate('.')` starts calculating the path from depends on the `<Route> (= pathnameBase)` that the component calling `useNavigate()` belongs to.

## The Symptom

On a detail page (path: `/list/:no`), I ran the code below to go back to the list (path: `/list`).

```ts
const navigate = useNavigate();

navigate('..'); // expected: /list
```

But instead of going to `/list`, it ended up navigating to the root path (path: `/`). I wondered whether navigate had run twice, so I added a console log to check, but `navigate` was called exactly once.

Even when I passed the `relative: path` option as shown below, the result was the same.

```ts
navigate('..', { relative: 'path' })
```

Every other component that uses the same code shows the same behavior.

## The Debugging Process

1. Number of navigate calls
	- What to check: First, suspect that it's being called twice due to event bubbling or a double click
	- How to check: Use console.count()
	- Result: Confirmed it was called only once
1. Route structure
	- What to check: Is the route declaration wrong? Check for absolute vs. relative paths, and whether a pathless wrapper (Route path="") exists
	- How to check: Inspect the `<Route path="...">` declarations
	- Result: No problem
1. Current URL
	- What to check: Is the URL right before the click definitely `/list/:no`? Did I unknowingly redirect to `/list`?
	- How to check: Log `location.pathname`
	- Result: No problem. The URL right before the click was `/list/:no`.
1. Comparing relative paths
	- What to check: Compare the behavior of navigate('.') vs navigate('..')
	- Result: **Contrary to what the official docs describe, navigate('.') worked exactly as expected**
	- Question: '.' worked as expected, but '..' did not. Isn't '.' supposed to navigate to the current route, and '..' to the parent route?
	- Checking the official docs: The official docs only describe the starting point vaguely. They say the current location is where `useNavigate` was called, but it's not clear whether that applies only when the `relative: "path"` option is set, or always.
1. Inspecting the useNavigate internals
	- What to check: How useNavigate works internally in the `react-router hooks.tsx` file
	- How to check:
		- [Looking at the code in react-router that uses matches to build the route structure into an array](https://github.com/remix-run/react-router/blob/main/packages/react-router/lib/hooks.tsx#L246C43-L246C62)
		- [The current route that useNavigate recognizes is the last element in the current component's matches array](https://github.com/remix-run/react-router/blob/main/packages/react-router/lib/router/utils.ts#L1430C55-L1430C73)
	- Result: The **calculation reference point** for `route navigation` is the last element (Route) of the matches array in the current component.
1. The route reference point
	- What to check: Use useMatches() to check the `pathnameBase` of the component that runs useNavigation
	- Result: `pathnameBase` shows up as `/list`(!)
	- Question: Why isn't `pathnameBase` `/list/:no`?

## The Cause

1. useNavigate() uses the `pathnameBase` of the `<Route>` that the component it's declared in belongs to as its reference point.

1. Even if you pass relative: 'path' in the `navigate` options, the calculation reference point does not change. It only changes the interpretation method to the "URL segment" approach.

1. If you call `useNavigate` from a higher-level route like a Layout and navigate, then even if you're looking at a detail page (`/list/:no`), the reference point becomes `/list`.

```tsx
<Route path="/list" element={<Layout />}> {/* ← useNavigate() inside Layout */}
	<Route index element={<List />} />
	<Route path=":no" element={<Content />} />
</Route>
```

4. Therefore, even applying `'..'` just once turns `/list` -> `/`. `/list/:no` was never part of the calculation in the first place. As a result, it **looks as if two segments were cut off at once**.

## How to Check the Current Path That useNavigate Recognizes

### Method 1: The simplest

In `React DevTools`, select the component that calls useNavigate -> in the hooks panel, check `Navigate.Route.matches[last].pathnameBase`

### Method 2

```ts
const Component = () => {
	// ...
	  // the array of matched routes (root → deepest)
  const matches = useMatches();

  // the route the current component belongs to = the last element of the array
  const current = matches[matches.length - 1];

  console.log('pathnameBase : ', current.pathnameBase); // e.g. "/list"
	// ...
}
```

### Method 3

```ts
// a handy method gpt told me about
import { useResolvedPath } from 'react-router-dom';

function usePathnameBase(): string {
  // the result of resolving "." (route-relative) is the pathnameBase
  const { pathname } = useResolvedPath('.');

  return pathname;          // e.g. "/list"
}
```

## Conclusion and Best Practices
- The `pathnameBase` of the component that calls `useNavigate` is always the routing reference point.
- When you're confused, using an absolute path for routing from the start is also a good approach
	- e.g. navigate('/list')
- When debugging routing, you need to debug the routing reference point.
	- Check `pathnameBase` with `useMatches()` / `useResolvedPath('.')`

## Summary
- Problem: I used `navigate('..)` but it goes up more levels than expected.

- Cause: Because navigate's starting point is the `<Route>` (pathnameBase) of the component that runs navigate. If the code that runs `navigate` is attached to a higher-level route, like a shared header or search bar, the detail URL segment doesn't get included in the calculation.

- Solutions
	- In a shared component on a higher-level route, use navigate('.') to navigate to the parent route, or
	- use an absolute path like navigate('/notice'), or
	- move the `useNavigate()` call down to a lower-level route, then run `navigate('..')` (to navigate to the parent route)

- Checkpoint: Always check pathnameBase via logs

EOD

20250430
