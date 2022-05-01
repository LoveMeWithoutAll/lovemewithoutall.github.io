---
layout: single
title: react-query mutation을 state store로 쓸 수 있을까?
date: 2022-04-29 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
    image: "https://react-query.tanstack.com/_next/static/images/logo-7a7896631260eebffcb031765854375b.svg"
categories:
- IT
tags: [javascript]
---

react-query의 useQuery는 state 저장소의 역할을 할 수 있다. useQuery의 return값은 어느 컴포넌트에서 조회하든 같은 값을 반환한다.

```typescript
// data가 store나 마찬가지다
const { data, isLoading, error, refetch } = useQuery(['query-key'], () => service.api());
```

그렇다면 useMutation도 어느 컴포넌트에서 return값을 조회하든 같은 값을 반환할까? 만약 그렇다면 CUD request에 대한 반환값을 저장하여 store처럼 사용할 수 있다. 하위 컴포넌트에 props로 값을 계속 넘기는 짓을 그만 해도 된다!

하지만 테스트 해보니 useMutation의 return값은 useQuery처럼 동일한 값을 반환하지 않았다. 

예를 들어보자. A컴포넌트에서 useMutation으로 mutate를 실행하고, B컴포넌트에서 useMutation의 data를 사용한다면, B컴포넌트의 data는 항상 undefined가 될 것이다. 다른 컴포넌트에서 실행하는 mutate는 다른 컴포넌트의 data에 영향을 미치지 않는다.

따라서 react-query를 state store로 사용하려면 제약이 있다. 데이터 조회는 get API로 만들어야 한다.

그 외: 내가 이런 삽질을 한 후, 공식 문서를 뒤져보니, 공식 문서에 링크된 블로그에 위 내용이 있었다...
- https://tkdodo.eu/blog/react-query-as-a-state-manager
- https://tkdodo.eu/blog/mastering-mutations-in-react-query

20220428
