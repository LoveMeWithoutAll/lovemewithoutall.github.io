---
layout: single
title: Vue에서 Boostrap와 jquery 사용하기
date: 2018-01-23 17:07:30.000000000 +09:00
type: post
header:
  teaser: "/assets/images/bootstrap-vue.png"
  image: "/assets/images/bootstrap-vue.png"
categories:
- IT
tags: [Vue.js, Bootstrap jQuery]
---

## 시작

### 몇 가지 방법

[Vue.js]에서 [Bootstrap]과 [jQuery]를 사용하려면 약간의 추가 작업이 필요하다. 여기엔 몇 가지 방법이 있다.

1. [Bootstrap Vue]에서 제공하는 vue cli template을 사용
1. [expose-loader] 사용: [이 글](http://vuejs.kr/jekyll/update/2017/03/02/vuejs-jquery-bootstrap/)을 참고할 것
1. [webpack]을 사용

### 선택

나는 [webpack]을 사용하는 방법이 깔끔했다. [이 글](https://brendaniel.github.io/2017/11/17/Vue에서-jquery와-bootstrap-전역으로-사용하기/)을 참고하여 약간 보완했다. 구조는 [vue cli]의 [webpack template](https://github.com/vuejs-templates/webpack)를 사용한 프로젝트 기준이다. 각 라이브러리의 버전은 다음과 같다.

1. [Bootstrap] v4.0.0
1. [jQuery] v3.3.1
1. [popper.js] v1.12.9

## 설치

```bash
npm install --save jquery bootstrap popper.js
```

## 설정

### webpack

```javascript
#build/webpack.base.conf.js

const webpack = require('webpack')

module.exports = {
  ...
  plugins: [
    new webpack.ProvidePlugin({
      $: 'jquery',
      jquery: 'jquery',
      'window.jQuery': 'jquery',
      jQuery: 'jquery'
    })
  ]
  ...
}
```

### eslint

```javascript
#.enlintrs.js

module.exports = {
  ...
  globals: {
    "$": true,
    "jQuery": true
  }
}
```

## import

```javascript
#src/main.js

import 'bootstrap'
import 'bootstrap/dist/css/bootstrap.min.css'
```

이제 예쁘게 개발해보자!

[Vue.js]: https://vuejs.org
[jQuery]: https://jquery.com
[Bootstrap]: http://getbootstrap.com
[Bootstrap Vue]: https://bootstrap-vue.js.org
[expose-loader]: https://github.com/webpack-contrib/expose-loader
[webpack]: https://webpack.js.org
[vue cli]: https://github.com/vuejs/vue-cli
[popper.js]: https://popper.js.org