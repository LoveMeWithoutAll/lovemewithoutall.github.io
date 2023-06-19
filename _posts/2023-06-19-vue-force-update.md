---
layout: single
title: Vue3 input의 model과 화면에 보이는 값이 불일치하면?
date: 2023-06-19 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/95/Vue.js_Logo_2.svg/1200px-Vue.js_Logo_2.svg.png"
categories:
- IT
tags: [Vue]
---

## 환경
* Vue 2.7
* `<script setup>` 문법 사용

## 문제
`v-model`로 선택값을 전달받는 평범한 `select` 박스를 만들었음. 특정 `option`을 선택하면 validation 함수가 동작하여 안내문과 함께 해당 `option`을 선택하지 못하는 비즈니스 로직을 구현하려고 함.

그런데 문제가 발생했다. validation 함수 잘 동작했고, v-model의 변경을 차단했는데... 화면에는 방금 선택한 그 `option` 값이 선택되어 있었다. v-model이 변경되지 않았으니 option값은 변경 이전의 그 값으로 선택되어야 하지 않은가?

`Vue` 인스턴스 내부를 들여다보니, v-model의 값은 내 의도대로 잘 변경되어 있었다. 하지만 v-model이 바인딩 된 select box에는 엉뚱한 값이 찍혀 있었다.

## 원인
* 1줄 요약: v-model이 변경되지 않았으니 rerender도 하지 않았기 때문이다.

1. select 태그의 value는 html dom api에서 제공하는 값
1. vue의 v-model로 input의 value(여기서는 selected)를 설정하면 그건 vue 인스턴스 내부의 값
1. vue는 vue 인스턴스 내부의 값이 변경될 때 rerender를 함
1. html dom api의 input value는 사용자가 입력했을 때 이미 변경되었음
1. 하지만 vue 인스턴스 내부의 값이 변경되지 않았으니 rerender를 하지 않음
1. vue의 v-model로 설정한 input value와 dom api의 input value가 따로 놀게 됨

## 해결책
여러 방법이 있지만, `key`를 변경하는 게 가장 깔끔한 방법이다.

```javascript
<template>
  <MyComponent :key="componentKey" />
</template>
<script setup>
import { ref } from 'vue';
const componentKey = ref(0);

const forceRerender = () => {
  componentKey.value += 1;
};
</script>
```

괜히 이상한 짓, 이를테면 `$forceUpdate` 같은 걸 시도하느라 시간 낭비하지 말자.

## 참고
* [Vue select 값이 안바뀜?](https://stackoverflow.com/questions/69279746/vue-3-binding-problem-with-select-if-option-selected-is-not-showing-in-the-ne)
* [Vue3 공식문서: forceUpdate](https://vuejs.org/api/component-instance.html#forceupdate)
* [Vue composite API의 내부 동작 & 왜 getInstance가 null을 뱉는지](https://4ark.me/post/87ba8d8b.html)
* [Vue를 강제로 rerender 하는 여러가지 방법](https://michaelnthiessen.com/force-re-render/)
