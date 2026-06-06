---
layout: single
title: Can react-query mutation be used as a state store?
date: 2022-04-29 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---

react-query's useQuery can play the role of a state store. The return value of useQuery returns the same value no matter which component queries it.

```typescript
// data is basically a store
const { data, isLoading, error, refetch } = useQuery(['query-key'], () => service.api());
```

So does useMutation also return the same value no matter which component queries its return value? If so, we could store the return value of a CUD request and use it like a store. We could stop the chore of constantly passing values down to child components as props!

But after testing, the return value of useMutation did not return the same value the way useQuery does.

Let's take an example. If component A runs mutate with useMutation, and component B uses useMutation's data, then component B's data will always be undefined. The mutate executed in one component does not affect the data in another component.

Therefore, there are constraints when using react-query as a state store. Data retrieval has to be done with a get API.

By the way: after I went through all this struggle, I dug through the official docs and found that the content above was in a blog linked from the official documentation...
- https://tkdodo.eu/blog/react-query-as-a-state-manager
- https://tkdodo.eu/blog/mastering-mutations-in-react-query

20220428
