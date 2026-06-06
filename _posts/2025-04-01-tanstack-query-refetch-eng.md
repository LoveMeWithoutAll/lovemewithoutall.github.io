---
layout: single
title: tanstack query's refetch forces a request to run
date: 2025-04-01 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
    image: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
categories:
- IT
tags: [tanstack query]
---

When using the useQuery hook from [tanstack query](https://tanstack.com/query/latest) (formerly react-query), the `enabled: false` setting is an option that prevents the initial query execution. But what happens when you use it together with `refetch()`?

## Key Summary
* `enabled: false` prevents the query from running automatically.
* However, `refetch()` forcibly runs the query manually, so it operates independently of `enabled: false`.
* Therefore, <b>even with `enabled: false`, calling `refetch()` triggers an HTTP request.</b>


### Example

```tsx
const { data, refetch } = useQuery(
  ['myData'],
  fetchData,
  {
    enabled: false,
  }
);

// Manually sends a request when some button is clicked
<button onClick={() => refetch()}>Load</button>
```

### Note: How refetch() behaves
* `refetch()` forces the query to run, regardless of `enabled`.
* Internally, it works similarly to calling `queryClient.fetchQuery()`.

20250401
