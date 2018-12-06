---
layout: single
title: 정규식으로 URL 쿼리스트링에서 특정 파라미터 추출
date: 2018-12-06 20:05:30.000000000 +09:00
type: post
header:
    teaser: "https://pbs.twimg.com/profile_images/851635058339336192/ZI5FHI72_400x400.jpg"
    image: "https://pbs.twimg.com/profile_images/851635058339336192/ZI5FHI72_400x400.jpg"
categories:
- IT
tags: [JavaScript]
---

Internet Explorer는 `URLSearchParams`를 지원하지 않는다. 그래서 URL 쿼리스트링을 다루기가 귀찮아진다.

`polyfill`을 넣거나 쿼리스트링을 뽑아내는 함수를 따로 만드는건 좋은 방법이지만, 상황에 따라 딱 한 줄의 코드로 내가 원하는 `key`의 `value`를 뽑아내고 싶을 때가 있다.

그래서 간단한 정규식과 `replace`문으로 쿼리스트링 중 특정 key의 value를 뽑아내는 코드를 만들어보았다.

```javascript
// 아래 코드의 'key'를 수정하여 원하는 value를 뽑아내자
var wantedParam = /key=[a-z]*/.exec(window.location.search)[0].replace("&key=", "").replace("key=", "");
```

다소 번잡하지만 잘 돌아간다.

* 참고: [URLSearchParams 브라우저 호환성](https://developer.mozilla.org/ko/docs/Web/API/URLSearchParams#%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80_%ED%98%B8%ED%99%98%EC%84%B1)
