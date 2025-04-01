---
layout: single
title: tanstack query의 refetch는 강제 request 수행이다
date: 2025-04-01 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
    image: "https://tanstack.com/_build/assets/splash-light-CHqMsyq8.png"
categories:
- IT
tags: [tanstack query]
---

[tanstack query](https://tanstack.com/query/latest) (구 react-query)의 useQuery 훅을 사용할 때, enabled: false 설정은 초기 쿼리 실행을 막는 옵션입니다. 그런데 refetch()와 함께 사용하면 어떻게 될까요?

## 핵심 요약
* enabled: false는 쿼리가 자동 실행되지 않도록 합니다.
* 하지만 refetch()는 강제로 쿼리를 수동 실행하므로, enabled: false와는 별개로 동작합니다.
* 따라서 <b>enabled: false여도, refetch()를 호출하면 HTTP 요청이 발생합니다.</b>


### 예시

```javascript
const { data, refetch } = useQuery(
  ['myData'],
  fetchData,
  {
    enabled: false,
  }
);

// 어떤 버튼 클릭 시 수동으로 요청 보냄
<button onClick={() => refetch()}>불러오기</button>
```

### 참고: refetch()의 동작
* refetch()는 enabled와 관계없이 강제 실행입니다.
* 내부적으로 queryClient.fetchQuery()를 호출하는 것과 유사하게 작동합니다.

20250401
