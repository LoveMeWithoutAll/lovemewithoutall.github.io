---
layout: single
title: webpack build 결과물 경로 설정하기
date: 2018-06-18 19:50:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
    image: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
categories:
- IT
tags: [Javascript, webpack]
---

이 글은 webpack 4.11.1 버전을 기준으로 합니다.

웹팩으로 빌드하면 dist 디렉토리 아래에 배포를 위한 결과물들이 만들어진다. 이 결과물들이 프로젝트의 루트 경로에 둘거라면 아무 문제 없다. 하지만 대형 프로젝트의 일부 모듈만 따로 작업하는 경우에는 webpack 설정을 조금 만져줘야 한다. build 결과물로 만들어진 index.html이 참조하는 static 파일의 경로 설정이 필요하다. webpack의 기본 설정은 static 파일의 경로를 프로젝트의 루트 경로 아래로 잡아두기 때문이다. 아래와 같이 바꾸면 된다.

```javascript
// config/dindex.js
'use strict'
// Template version: 1.3.1
// see http://vuejs-templates.github.io/webpack for documentation.

const path = require('path')

module.exports = {
    // ...
    build: {
        // ...
        // assetsPublicPath를 원하는 경로로 바꿔주면 된다. 기본값은 / 이다
        assetsPublicPath: '/survey/dist/', // 예를 들면 이렇게...
        // ...
    }
    // ...
}
```

끝!