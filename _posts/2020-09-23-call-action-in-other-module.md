---
layout: single
title: Vuex에서 다른 모듈의 action 호출하기
date: 2020-08-21 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://cli.vuejs.org/favicon.png"
    image: "https://cli.vuejs.org/favicon.png"
categories:
- IT
tags: [JavaScript, Vue.js, Vuex]
---

`dispatch`를 쓰면 된다.

```javascript
const callActionInOtherModule = async (store, { payload }) => {
  try {
    store.dispatch("moduleB/actionName", { payload }, { root: true });
  } catch (e) {
    console.error(e);
  }
};
```

`{ root: true }`를 꼭 넣어야 한다.
