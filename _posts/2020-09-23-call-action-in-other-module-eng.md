---
layout: single
title: Calling an Action in Another Module from Vuex
date: 2020-09-23 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://cli.vuejs.org/favicon.png"
    image: "https://cli.vuejs.org/favicon.png"
categories:
- IT
tags: [JavaScript, Vue.js, Vuex]
---

You can use `dispatch`.

```javascript
const callActionInOtherModule = async (store, { payload }) => {
  try {
    store.dispatch("moduleB/actionName", { payload }, { root: true });
  } catch (e) {
    console.error(e);
  }
};
```

Make sure to include `{ root: true }`.
