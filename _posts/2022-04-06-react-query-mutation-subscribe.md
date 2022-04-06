---
layout: single
title: react-query mutationCache subscribe 사용 예시
date: 2022-04-06 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---


react-query에서 특정 mutation을 수행하고, mutation이 완료될 경우 특정 함수를 실행하려면 어떻게 해야할까?
react-query는 mutation을 수행할 때마다 mutationCache를 update한다.
따라서 mutationCache의 변경을 subscribe 하고 있다가, 특정 Mutation의 수행이 완료되면 원하는 함수를 실행하면 된다.

예시는 이렇다.

```javascript
import { useEffect } from 'react';
import { useQueryClient } from 'react-query';

const useCustomHook = () => {
  const queryClient = useQueryClient(); // react-query에서 queryClient를 뽑는다
  const mutationCache = queryClient.getMutationCache(); // mutationCache를 뽑는다.

  useEffect(() => {
    // 구독 콜백을 설정한다
    const unsubscribe = mutationCache.subscribe(event => {
      if (event?.state.status !== 'success') return; // mutation이 성공한 경우에만 처리
      if (원하는 변경사항인지 확인) doWhatYouWant(); // 이제 원하는 일을 한다.
    });

    return unsubscribe; // 컴포넌트가 죽을 때 구독을 끊어준다
  }, []);
};
```

물론 useMuation의 option에 onSettled 등의 콜백을 등록하면 가장 좋겠으나, 부득이 할 경우 위 방법을 사용하면 편리하다

20220310
