---
layout: single
title: 웹팩으로 다른 프로젝트에서 번들링 한 파일을 가져다 쓰기
date: 2021-11-23 01:17:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
    image: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
categories:
- IT
tags: [webpack]
---

다른 프로젝트에서 번들링 한 output 파일을 react 등 다른 frontend framework에서 가져다 쓸 수 있을까? 물론 이런 걸 하기 위해 모듈화를 하는 거지만, 간혹 레거시와 함께 해야 할 필요가 있을 때가 있다. [craco](https://github.com/gsoft-inc/craco)를 쓰면 간단하다.

`craco.config.js`에 다음과 같이 설정한다.

```javascript
module.exports = {
  webpack: {
    module: {
      rules: [
        {
          test: /\host.three.js$/, // test에 import할 js 파일을 지정하고
          use: 'bundle-loader' // bundle-loader를 사용한다고 룰을 새긴다
        }
      ]
    }
  }
}
```

JavaScript에 다음과 같이 import한다.

```javascript
import HOST from '../../host.three'
```

이제 번들링 된 js파일을 사용 가능.

20211024
