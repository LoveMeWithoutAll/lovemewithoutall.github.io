---
layout: single
title: webpack에서 디버깅 환경 구성하기
date: 2018-03-12 08:07:30.000000000 +09:00
type: post
header:
  teaser: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
  image: "https://camo.githubusercontent.com/d18f4a7a64244f703efcb322bf298dcb4ca38856/68747470733a2f2f7765627061636b2e6a732e6f72672f6173736574732f69636f6e2d7371756172652d6269672e737667"
categories:
- IT
tags: [Vue.js, debug, webpack, vue-cli]
---

## webpack 시작
[Vue.js] 등의 프론트엔드 프레임워크를 사용해서 개발을 시작하면 [webpack] 사용을 피할 수 없다. 
[webpack]에서는 [webpack-dev-server]를 제공하는데, 개발시에 로컬 [웹서버](https://developer.mozilla.org/ko/docs/Learn/Common_questions/What_is_a_web_server) 역할을 해준다. 
[webpack]의 설정은 상당히 복잡하지만, 대부분의 프론트엔드 프레임워크는 자신만의 생태계에 [vue-cli]와 같은 명령줄 도구에서 알아서 구성해주는대로 사용하면 쉽게 웹서버를 올릴 수 있다. 
이하는 [vue-cli]를 기준으로 설명하겠다. 다른 프레임워크도 똑같다. 

## 프론트엔드 디버깅 환경 구성
대개 webpack의 기본 환경 구성만으로 브라우저의 개발자도구를 사용해 디버깅이 가능할 것이다. 다만 조금 혼동스러운 것이 있다. 
자바스크립트 디버깅을 할 때, 기존에 하던대로 localhost의 소스코드에 break point를 잡을 수 없다. 
webpack의 source-map에 연결된 코드에 break point를 잡아야 디버깅이 가능하다. 
아래 화면의 화살표가 가리키는 코드처럼 **webpack://** 아래의 코드에 break point를 잡자.

![webpack frontend debug](/assets/images/webpack-frontend-debug.png)

만약 이게 안된다면 [webpack-dev-server]가 디버깅용으로 설정되지 않은 것이다. 
**devtool** 설정을 바꿔주면 된다.

```javascript
// config/index.js
devtool: 'cheap-module-eval-source-map'
```

**devtool** 설정에는 *cheap-module-eval-source-map* 외에도 몇 가지 선택지가 있다. [공식 문서](https://webpack.js.org/configuration/devtool/#devtool)와 [(Webpack) devtool 옵션 퍼포먼스](https://blog.perfectacle.com/2016/11/14/webpack-devtool-option-performance/)를 참조해서 설정하자.

## 백엔드 디버깅 환경 구성
가난한 풀스택 개발자라면 로컬 환경에서 프론트엔드와 백엔드 서버를 다 띄워놓고 개발해야 한다. 이 때 별다른 설정을 안했다면 프론트엔드에서 백엔드로 http request가 보내지지 않는다. http request를 프론트엔드 서버(이 경우에는 [webpack-dev-server]가 로컬 환경에 띄워준 웹서버)로 보내기 때문이다. request를 백엔드로 보내야 한다. 아래 예시를 참고하여 webpack에 **proxyTable**을 설정해준다.

```javascript
// config/index.js
    proxyTable: {
      '/api': {
        target: 'http://localhost:내_백엔드_포트/api',
        changeOrigin: true,
        pathRewrite: {
          '^/api': ''
        }
      }
    }
```

위 예시에서는 **proxyTable**이라는 이름이지만, [vue-cli]에서 [webpack] 구성을 그렇게 해놓은 것이고, [webpack]의 [공식 문서](https://webpack.js.org/configuration/dev-server/#devserver-proxy)를 비롯하여 대개 **proxy**라는 이름으로 설정해준다. 환경에 따라 적당히 사용하자. 
이 기술에 대해 더 자세히 알고 싶다면 [http-proxy-middleware](https://github.com/chimurai/http-proxy-middleware)를 읽어보자. 

[webpack]: https://webpack.js.org/
[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md
[webpack-dev-server]: https://webpack.js.org/configuration/dev-server/