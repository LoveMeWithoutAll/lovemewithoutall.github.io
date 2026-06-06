---
layout: single
title: An example of using react-query mutationCache subscribe
date: 2022-04-06 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---


In react-query, how do you run a specific function once a particular mutation has been performed and completed?
react-query updates the mutationCache every time a mutation is performed.
So you can subscribe to changes in the mutationCache, and when the specific mutation has completed, run whatever function you want.

Here is an example.

```javascript
import { useEffect } from 'react';
import { useQueryClient } from 'react-query';

const useCustomHook = () => {
  const queryClient = useQueryClient(); // get the queryClient from react-query
  const mutationCache = queryClient.getMutationCache(); // get the mutationCache

  useEffect(() => {
    // set up the subscription callback
    const unsubscribe = mutationCache.subscribe(event => {
      if (event?.state.status !== 'success') return; // only handle when the mutation succeeds
      if (check whether it is the change you want) doWhatYouWant(); // now do what you want
    });

    return unsubscribe; // unsubscribe when the component unmounts
  }, []);
};
```

Of course, registering a callback such as onSettled in the useMutation options would be the best approach, but when that is not possible, the method above is handy.

20220310
