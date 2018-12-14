---
layout: single
title: Vue.js 프로젝트 구조 - views와 components의 차이
date: 2018-12-14 17:05:30.000000000 +09:00
type: post
header:
    teaser: "https://kr.vuejs.org/images/logo.png"
    image: "https://kr.vuejs.org/images/logo.png"
categories:
- IT
tags: [Vue]
---

누군가에게 설명해주려는데 갑자기 기억이 나지 않아서 당황했다. 그래서 정리를 해보았다.

[vue-cli]로 프로젝트를 시작하면 햇갈리는 폴더가 있다. `views` 폴더와 `components` 폴더가 그것인데, 둘 다 `aaa.vue` 포맷으로 Vue component 파일이 들어있다. 그렇다면 `views`폴더와 `components`폴더는 무엇이 다를까? 답은 간단하다.

## `router`에서 보여주는 component 파일은 `views` 폴더에 넣는다. 그 외에는 `components` 폴더다.

물론 폴더 구조는 어떻게 하든 상관없다. 위의 구분은 [vue-cli]가 제시하는 예시일 뿐이다. 좋을대로 하시라!

* 참고: https://stackoverflow.com/questions/50865828/what-is-the-difference-between-the-views-and-components-folders-in-a-vue-project

[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md
