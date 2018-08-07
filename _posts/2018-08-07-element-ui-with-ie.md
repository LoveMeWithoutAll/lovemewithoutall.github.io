---
layout: single
title: Internet Explorer에서 element-ui 사용하기(한글 입력 오류)
date: 2018-07-26 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
    image: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
categories:
- IT
tags: [Vue.js, JavaScript]
---

## 문제

v2.4.5 기준으로 IE(Internet Explorer)11에서 [element-ui]를 사용하면 아래와 같은 오류가 뜬다. 한글 입력이 잘 안된다.

![korean-input-error](https://user-images.githubusercontent.com/8110371/41639651-a22c6916-7499-11e8-93d8-cebf80eafb79.gif)

[Google Chrome]에서는 물론(?) 잘 된다.

![normal-case](https://user-images.githubusercontent.com/8110371/41639648-9f2073e8-7499-11e8-95a8-3ab6c1397107.gif)

## 원인

[element-ui]는 중국어 사용자 위주로 만들어져 있다. default 언어가 중국어로 되어 있을 정도다. 그러다보니 form의 input box도 중국어가 먼저인데, input box에 입력하는 한 글자가 완성되기 전까지 기다리는 로직이 있다. 이게 IE에서 오작동하는 것.

## 해결책

[여기](https://github.com/jhlee8804/element/commit/0c1d0b3d66c30d3182534a20ea706c951424e3a7?diff=unified)를 참고해 지우면 된다. **valueBeforeComposition** 와 관련된 코드를 다 지워버리자. 2번 방법을 추천한다.

### 1. node_modules 직접 수정

내 프로젝트의 node_modules 디렉토리에서 아래 파일들을 수정하면 된다.

* /node_modules/element-ui/lib/input.js
* /node_modules/element-ui/lib/element-ui.common.js

### 2. element-ui 소스 수정한 후, 배포판 빌드

[element-ui github 저장소](https://github.com/ElemeFE/element)에서 clone 받은 후, *packages/input/src/input.vue* 파일을 수정한다. 그리고 *npm run dist*를 날려주고 그 결과물을 내 프로젝트에 넣는다.

## 그 외

[JiHyung Lee](https://github.com/jhlee8804)님이 위 문제를 해결하는 [contribute](https://github.com/ElemeFE/element/issues/11665)를 하셨으니 다음 버전에서는 해결되리라 믿는다.

물론 polyfill은 필수다. [이 글](https://lovemewithoutall.github.io/it/vue-ie-support-with-es6-promise/)을 참조하시라.

끝!

[element-ui]: https://github.com/ElemeFE/element
[Google Chrome]: https://www.google.com/chrome/