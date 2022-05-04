---
layout: single
title: react-query 에러 처리시 주의할 점
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

react-query의 useQuery로 데이터를 조회할 때, catch 구문에서 처리를 누락한 타입의 오류가 있다면?
QueryCache의 subscribe에서 오류를 인식하지 못한다.
response status code를 기준으로 try catch를 하려면 반드시 모든 code 케이스의 처리를 해주든가, 아니면 아예 catch를 하지 말아야 한다. catch를 하지 않으면 react-query의 내부적으로 돌아가는 에러 처리 로직을 탄다. 나는 가급적 try catch를 쓰지 말고, onError 콜백 등을 사용할 것을 권한다. 괜히 버그의 여지를 둘 필요가 없다.

```typescript
// BAD
const { data, refetch } = useQuery<response>(
    'key',
    () => service.get().catch(err => {
        if (err.status === 400) return null; // 400 error만 처리됨. 나머지는 무시
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
            throw err; // 반드시 모든 케이스에 대한 error 처리를 해줘야 queryCache가 오류를 인식한다
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
      const error = event?.query?.state?.error; // catch에서 throw한 error만 잡아냄
    });

    return unsubscribe;
}, []);
```

중요하지만 공식문서에 설명되지 않은 사실이다. react-query 소소코드를 까뒤집어 알아냈다. react-query 사용자는 유의해야 한다.

---

2.

onSettled와 onError옵션이 모두 설정되어 있는 경우, onError -> onSettled 순서로 처리된다.


---

3.

가급적이면 QueryCache를 쓰든, MutationCache를 쓰든, subscribe로 공통 로직을 넣으려는 시도는 하지 말아야 한다. 공식 문서에 나와 있지 않은 상태값을 기준으로 작업해야 하기 때문이다.

---

4.

QueryCache는 queryKey만 같다면 error response도 캐싱한다. 그러니 같은 key로 query를 실행하면 캐싱되어 있던 error response를 사용한다.  별다른 설정을 하지 않았다면 캐시된 값을 사용하는 동시에 API request를 보낸다. 이 때 서버 불안정 등의 이유로 API response가 200 ok와 500 error를 오가고 있다면, 캐싱은 불필요하다. 그러니 API error가 발생할 경우, 캐시 삭제를 추천한다.

이렇게 지우면 된다.

```typescript
const queryClient = useQueryClient();
queryClient.clear(); // 모든 key의 캐싱이 다 삭제된다. error난 캐싱만 삭제하는 법은 따로 글을 쓰겠음
```

이건 MuatationCache도 마찬가지다. 그래서 put, patch, delete를 할 때 서버 오류가 발생하면, 그 오류를 캐싱할 수도 있다. 지워버리자

```typescript
const queryClient = useQueryClient();
const mutationCache = queryClient.getMutationCache();
mutationCache.clear();
```

---

5.

subscribe 사용시 실수할 가능성이 잦다.

subscribe를 2번 하면 2번 동작한다. 당연한 일이다. 그러니 1번만 subscribe에 등록하면 된다. 하지만 문제는 실수에서 발생한다. 이런 종류의 실수를 깨닫는 데에는 시간이 많이 걸린다. 실수로 2번 같은 함수를 실행하도록 subscribe를 등록하면, 함수는 2번 실행된다.  놀랍게도 나는 이 한 번의 실수로 며칠 이상의 시간을 써버렸다. 애꿎은 react-query의 소스코드를 뒤지며...

---

6.


mutation은 query와 달리 react-query에서 캐싱을 안하는 것처럼 공식 문서에 쓰여있지만, 실은 캐싱을 한다. MutationCache 객체를 통해 사용할 수 있다. 하지만 공식 문서에는 mutationCache에 대해 사용 방법도, 추천도, 비추천도 하지 않고 있다. useMutation의 callback 함수 실행을 위해 내부적으로 사용하려고 만든 것 같은데, 기왕 만든 API를 공개하지 않으려니 아까워서 이런 기행을 벌이는 게 아닌가 추측할 뿐이다.

```typescript
const queryClient = useQueryClient();
const mutationCache = queryClient.getMutationCache();
```

---
20220330
