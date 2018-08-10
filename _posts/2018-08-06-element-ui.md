---
layout: single
title: element-ui 사용 소감
date: 2018-08-06 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
    image: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
categories:
- IT
tags: [Vue.js, JavaScript]
---

지난 주에 [vuetify]를 사용하여 서비스를 하나 만들었다. [vuetify]의 번잡함(?)에 질려(?), 비교적 깔끔해보이는 [element-ui]를 사용하여 서비스를 만든 후, 후기를 적어보았다.

## 장점

1. 예쁘다.
1. 문서가 대단히 정갈하게 잘 되어있다.
1. [Bootstrap] 만큼이나 배우기 쉽다.

## 단점

1. v2.4.5 기준으로 IE(Internet Explorer) 사용시, form에서 한글 입력이 잘 안된다(!). 물론 [element-ui]의 소스코드를 수정하면 해결할 수 있긴 하다. [이 글](https://lovemewithoutall.github.io/it/element-ui-with-ie/)을 참고하여 고치면 된다.
1. [vuetify] 등 다른 UI Framework와 비교해 없는 컴포넌트가 몇 개 있다(divider 라든지...)
1. 놀랍게도 default 언어 설정이 중국어다. 그래서 달력을 영어로 보려면 i18n으로 언어 설정을 영어로 잡아줘야한다.
1. [Vue CLI]와 연계가 안되어 있다.

## 총평

1. 다른 [Material Design] 계열 UI Framework의 github star를 몇 배나 능가할만큼 좋은지는 모르겠다.
1. 웹브라우저를 컨트롤 할 수 없는 조건이라면 사용하기 꺼려진다. IE에서 한글 입력이 잘 안되니까...

끝!

[Bootstrap]: http://getbootstrap.com
[Material Design]: https://material.io/design/
[vuetify]: https://github.com/vuetifyjs/vuetify
[Vue CLI]: https://cli.vuejs.org/
[element-ui]: https://github.com/ElemeFE/element
[Vue.js]: https://vuejs.org/
