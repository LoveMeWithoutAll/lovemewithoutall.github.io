---
layout: single
title: Vue 컴포넌트 깊숙히 숨어있는 DOM을 찾자
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

## 상황

Vue 컴포넌트 깊숙히 위치한 dom을 찾고 싶다.

## 방법

`ref`에서도 `querySelector`를 사용할 수 있다.

```typescript
<template>
  <NumberInput ref="inputRef" /> // input tag가 들어있는 컴포넌트
</template>

<script setup lang="ts">
import { ref } from 'vue';

const inputRef = ref<HTMLInputElement | null>(null);

const getInputTag = () => inputRef.value.$el.querySelector('input'); // DOM API의 querySelector와 동일하게 사용하면 된다.
</script>
```

## 활용

컴포넌트 내부 input 태그에 focus를 맞추자.

```typescript
<template>
  <NumberInput ref="inputRef" /> // input tag가 들어있는 컴포넌트
</template>

<script setup lang="ts">
import { nextTick, ref } from 'vue';

const inputRef = ref<HTMLInputElement | null>(null);

const getInputTag = () => inputRef.value.$el.querySelector('input'); // DOM API의 querySelector와 동일하게 사용하면 된다.

const focusOnInput = () => {
  getInputTag().focus(); // input tag에 focus를 넣는다
}

nextTick(() => focusOnInput()); // 렌더링 된 후에 Focus를 맞춘다
</script>
```

----

후기: 그냥 리액트 쓰고싶다