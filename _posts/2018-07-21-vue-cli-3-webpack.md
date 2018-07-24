---
layout: single
title: Vue CLI 3에서의 Webpack 설정
date: 2018-07-19 17:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/vue-cli-3.png"
    image: "/assets/images/vue-cli-3.png"
categories:
- IT
tags: [Vue, JavaScript]
---

[Vue CLI 3](https://cli.vuejs.org/)에서는 webpack 설정 방법이 크게 바뀌었다. [Vue CLI 2](https://github.com/vuejs/vue-cli/tree/v2#vue-cli)와는 달리 따로 설정을 하지 않으면 webpack 설정 파일이 만들어지지도 않는다. 하지만 [이런](https://lovemewithoutall.github.io/it/webpack-dist-path/) [저런](https://lovemewithoutall.github.io/it/webpack-config-for-debug/) 이유로 webpack 설정을 만져줘야 할 때가 있다. 

설정 방법은 2가지다.

1. [vue ui]에서 설정
1. vue.config.js를 직접 수정

vue.config.js는 프로젝트의 루트 경로에 만들어진다. [vue ui]에서 설정해도 어짜피 vue.config.js를 변경하는거니 결과에 차이는 없다. 하기 설명은 두 방법의 사용 예시다.

## 1. build 결과물 경로 설정

[dist 폴더를 설정](https://lovemewithoutall.github.io/it/webpack-dist-path/)하려면 [vue ui]에서 하기 화면의 설정을 변경해주면 된다. 화살표가 가리키는 칸의 내용이 변경된 dist 폴더 경로다.

![vue ui](/assets/images/2018-07-21-vue-cli-3-webpack-config/output-dir-setting.png)

변경하고 저장하면 vue.config.js에 반영이 된다. vue.config.js가 아직 없다면, 새 파일이 만들어지면서 반영이 된다.

## 2. proxyTable 설정

로컬 개발환경에서 프론트엔드와 백엔드 서버가 분리되지 않았다면, [여기에 나온 것처럼](https://lovemewithoutall.github.io/it/webpack-config-for-debug/#%EB%B0%B1%EC%97%94%EB%93%9C%20%EB%94%94%EB%B2%84%EA%B9%85%20%ED%99%98%EA%B2%BD%20%EA%B5%AC%EC%84%B1) proxy middleware를 설정해야 한다.

그냥 vue.config.js 파일을 아래와 같이 수정해주면 된다. **pickpu_inter/api**라 박혀있는 경로는 적당히 바꾸자.

```javascript
'use strict'

module.exports = {
  outputDir: '/pickup_inter/dist', // 이건 위에서 설정한 dist 경로
  devServer: {
    proxy: { // proxyTable 설정
      '/pickup_inter/api': {
        target: 'https://백엔드-서버-URI/pickup_inter/api',
        changeOrigin: true,
        pathRewrite: {
          '^/pickup_inter/api': ''
        }
      }
    }
  }
}

```

끝!

## 참조

https://cli.vuejs.org/config/#devserver-proxy

[vue ui]: https://lovemewithoutall.github.io/it/vue-cli-3/