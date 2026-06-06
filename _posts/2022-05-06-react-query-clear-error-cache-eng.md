---
layout: single
title: How to clear only the error cache in react-query
date: 2022-05-04 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---

Let's make the QueryCache stop caching error response bodies.

```typescript
useEffect(() => {
  const errorsKeys = queryClient.getQueryCache()
                                .getAll() // from react-query's query cache
                                .filter(q => q.state.status === 'error') // pick only the ones that cached an error
                                .map(e => e.queryKey); // and extract just the queryKey

  return () => {
    queryClient.invalidateQueries(errorsKeys); // when the API Error modal closes, delete only the cached error responses
  };
}, [queryClient]);
```


20220419
