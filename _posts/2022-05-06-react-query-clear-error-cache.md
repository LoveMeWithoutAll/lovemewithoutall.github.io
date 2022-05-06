---
layout: single
title: react-query에서 오류가 발생한 cache만 삭제하는 법
date: 2022-05-04 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---

QueryCache에서 error response body를 캐싱하지 않도록 하자.

```typescript
useEffect(() => {
  const errorsKeys = queryClient.getQueryCache()
                                .getAll() // react-query의 query cache에서
                                .filter(q => q.state.status === 'error') // error를 캐싱한 것만 골라
                                .map(e => e.queryKey); // queryKey만 뽑아낸다

  return () => {
    queryClient.invalidateQueries(errorsKeys); // API Error 모달이 닫힐 때, 캐싱된 error response만을 삭제한다
  };
}, [queryClient]);
```


20220419
