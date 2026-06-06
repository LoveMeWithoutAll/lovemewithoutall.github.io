---
layout: single
title: Things to Watch Out for When Handling Errors in react-query
date: 2022-05-04 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---

1. 

When you fetch data with react-query's useQuery, what happens if there's an error of a type whose handling you missed in the catch block?
QueryCache's subscribe won't recognize the error.
If you want to use try/catch based on the response status code, you must either handle every single status code case, or not catch at all. If you don't catch, the error will flow through react-query's internal error-handling logic. I recommend avoiding try/catch as much as possible and using callbacks like onError instead. There's no reason to leave room for bugs unnecessarily.

```typescript
// BAD
const { data, refetch } = useQuery<response>(
    'key',
    () => service.get().catch(err => {
        if (err.status === 400) return null; // Only 400 errors are handled. The rest are ignored
    },
    {
      refetchOnWindowFocus: false,
      refetchOnMount: false,
      refetchOnReconnect: false,
    },
);

// GOOD
const { data, refetch } = useQuery<response>(
    'key',
    () => service.get().catch(err => {
        if (err.status === 400) {
            return null;
        } else {
            throw err; // You must handle errors for every case so that queryCache recognizes the error
        }
    },
    {
      refetchOnWindowFocus: false,
      refetchOnMount: false,
      refetchOnReconnect: false,
    },
);

const queryClient = useQueryClient();
const queryCache = queryClient.getQueryCache();

useEffect(() => {
    const unsubscribe = queryCache.subscribe(event => {
      const error = event?.query?.state?.error; // Only catches errors thrown in catch
    });

    return unsubscribe;
}, []);
```

This is an important fact that isn't explained in the official docs. I figured it out by digging through react-query's source code. react-query users should keep this in mind.

---

2.

When both the onSettled and onError options are set, they run in the order onError -> onSettled.


---

3.

As much as possible, you shouldn't try to inject common logic via subscribe, whether you're using QueryCache or MutationCache. That's because you'd have to work based on state values that aren't documented in the official docs.

---

4.

QueryCache caches error responses too, as long as the queryKey is the same. So when you run a query with the same key, it uses the cached error response. Unless you've configured something special, it sends an API request at the same time as it uses the cached value. At that point, if the API response is bouncing back and forth between 200 OK and 500 error due to reasons like server instability, caching is unnecessary. So when an API error occurs, I recommend clearing the cache.

You can clear it like this.

```typescript
const queryClient = useQueryClient();
queryClient.clear(); // Caching for all keys gets deleted. I'll write a separate post on how to delete only the errored caches
```

The same goes for MutationCache. So when a server error occurs during a put, patch, or delete, it might cache that error. Let's wipe it.

```typescript
const queryClient = useQueryClient();
const mutationCache = queryClient.getMutationCache();
mutationCache.clear();
```

---

5.

It's easy to make mistakes when using subscribe.

If you subscribe twice, it runs twice. That's obvious. So you just need to register the subscription once. But the problem arises from mistakes. It takes a lot of time to notice this kind of mistake. If you accidentally register a subscription so that the same function runs twice, the function runs twice. Amazingly, this single mistake cost me several days. Digging through react-query's blameless source code all the while...

---

6.


Unlike queries, the official docs make it sound like react-query doesn't cache mutations, but it actually does. You can access it through the MutationCache object. However, the official docs neither explain how to use mutationCache, nor recommend it, nor discourage it. It seems they created it for internal use, to run the callback functions of useMutation. I can only guess they ended up doing something this odd because, having gone to the trouble of building the API, they couldn't bear not to expose it.

```typescript
const queryClient = useQueryClient();
const mutationCache = queryClient.getMutationCache();
```

---
20220330
