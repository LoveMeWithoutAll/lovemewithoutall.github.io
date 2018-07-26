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

[Vue CLI 3]에서는 webpack 설정 방법이 크게 바뀌었다. [Vue CLI 2]와는 달리 따로 설정을 하지 않으면 webpack 설정 파일이 만들어지지도 않는다. 하지만 [이런](https://lovemewithoutall.github.io/it/webpack-dist-path/) [저런](https://lovemewithoutall.github.io/it/webpack-config-for-debug/) 이유로 webpack 설정을 만져줘야 할 때가 있다. 

설정 방법은 2가지다.

1. [vue ui]에서 설정
1. vue.config.js를 직접 수정

vue.config.js는 프로젝트의 루트 경로에 만들어진다. [vue ui]에서 설정해도 어짜피 vue.config.js를 변경하는거니 결과에 차이는 없다. 하기 설명은 두 방법의 사용 예시다.

## 1. build 결과물 경로 설정

[dist 폴더를 설정](https://lovemewithoutall.github.io/it/webpack-dist-path/)하려면 [vue ui]에서 하기 화면의 설정을 변경해주면 된다. 화살표가 가리키는 Output directory 칸의 내용이 변경된 dist 폴더 경로다.

![vue ui](/assets/images/2018-07-21-vue-cli-3-webpack-config/output-dir-setting.png)

위 화면처럼 Output directory를 설정하면 이렇게 하면 해당 vue-cli 프로젝트의 루트 경로에서 pickup/dist 폴더가 새로 생성되고, 그 아래에 build 결과물이 만들어진다.

[vue ui]의 settings를 변경하고 저장하면 vue.config.js에 반영이 된다. vue.config.js가 아직 없다면, 새 파일이 만들어지면서 반영이 된다.

**참조**: https://cli.vuejs.org/config/#outputdir

## 2. URL 설정

전체 서비스의 일부만 Vue로 바꾸는 경우에는 서비스의 entry point로 URL을 따로 잡아줘야 한다. 그렇지 않으면 build 결과 만들어지는 index.html에서 js와 css를 불러오지 못한다. 엉뚱하게 프로젝트의 루트 경로에서 js와 css를 찾고 있으니 잘 될 리가... [Vue CLI 2]에서는 기본적으로 index.html의 상대 경로로 js와 css의 위치가 잡혀 따로 설정을 안해줘도 됐던 것 같은데(?), [Vue CLI 3]에서는 설정을 해줘야 한다. 

설정 방법은 1번 항목 화면의 Base URL setting을 수정하면 된다. 설정 방법에는 2가지가 있다.

### 1. 빈칸으로 두기

Base URL setting의 default값은 **/** 이다. 이걸 지워버리고 해당 입력란을 빈칸으로 둔다. 이러면 index.html 파일이 불러오는 js와 css가 상대경로로 지정된다고 하는데, Vue-CLI v3.0.0-rc.5 기준으로는 위의 build 결과물 경로(보통 dist 일 것이다) 안에 html과 js, css가 하위 디렉토리 구분 없이 한꺼번에 만들어져 있는 모습을 보게 될 것이다. 모양새는 안 좋지만 아무튼 돌아간다.

### 2. URL 명시적으로 지정하기

index.html이 참조할 js와 css의 URL을 명시적으로 지정하면 build output도 예쁘게 나오고 마음이 편하다. [vue ui]의 Configuration에서 Base URL 입력란에 경로를 입력하자. 위 캡쳐 화면에서 나는 */pickup_inter/dist* 라 입력했다. 이 의미는 index.html이 참조하는 js와 css가 **Vue로_만든_서비스의_URL/pickup_inter/dist** 아래로 잡힌다는 뜻이다.

**참조**: https://cli.vuejs.org/config/#baseurl

## 3. proxyTable 설정

로컬 개발환경에서 프론트엔드와 백엔드 서버가 분리되지 않았다면, [여기에 나온 것처럼](https://lovemewithoutall.github.io/it/webpack-config-for-debug/#%EB%B0%B1%EC%97%94%EB%93%9C%20%EB%94%94%EB%B2%84%EA%B9%85%20%ED%99%98%EA%B2%BD%20%EA%B5%AC%EC%84%B1) proxy middleware를 설정해야 한다.

그냥 vue.config.js 파일을 아래와 같이 수정해주면 된다. **pickpu_inter/api**라 박혀있는 경로는 적당히 바꾸자.

```javascript
'use strict'

module.exports = {
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

**참조**: https://cli.vuejs.org/config/#devserver-proxy

끝!

[vue ui]: https://lovemewithoutall.github.io/it/vue-cli-3/
[Vue CLI 2]: https://github.com/vuejs/vue-cli/tree/v2#vue-cli
[Vue CLI 3]: https://cli.vuejs.org/