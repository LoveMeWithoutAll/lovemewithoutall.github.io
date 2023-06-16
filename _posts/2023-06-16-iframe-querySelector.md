---
layout: single
title: iframe의 js 코드에서 부모창의 dom을 찾으려면
date: 2023-06-16 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn.pixabay.com/photo/2013/07/13/11/50/sand-158804_1280.png"
    image: "https://cdn.pixabay.com/photo/2013/07/13/11/50/sand-158804_1280.png"
categories:
- IT
tags: [javascript]
---

iframe의 js 코드에서 부모창의 dom을 찾으려면 `parent.document` 객체에서 검색해야 한다.

```javascript
parent.document.querySelector('what you want');
```

브라우저 개발자 도구에서는 `window` 객체에서 검색해도 잘 나오지만, iframe에서는 그런 식으로 dom을 검색했다가는 밑도 끝도 없는 `null` 검색 결과를 만나게 될 것이다. 

~ 오늘의 1시간짜리 삽질 끝 ~

20230616
