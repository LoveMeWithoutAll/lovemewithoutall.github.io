---
layout: single
title: Android KitKat 이하 버전에서 Vue.js 웹뷰를 돌리자
date: 2018-11-05 14:03:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/support-old-android-on-vue.png"
    image: "/assets/images/support-old-android-on-vue.png"
categories:
- IT
tags: [android, Vue.js]
---

## 사건

내가 담당하는 안드로이드 하이브리드 앱이 하나 있는데, 얼마 전 특정 페이지 하나에 [Vue.js]를 적용했다. 쾌적하게 잘 돌아가는줄 알았는데, 오늘 오후에 오류 제보가 들어왔다. 확인해보니 [Vue.js]를 적용한 부분의 JavaScript가 안돌아가고 있었다. 상세한 정보는 다음과 같다.

* 오류 발생 기기: LG GPad 8.0 LTE
* 안드로이드 버전: KitKat 4.4.2

얼핏 보기에 오래된 안드로이드 버전이라 JavaScript를 제대로 실행하지 못하는게 아닌가 싶었다. 다행히 KitKat 버전부터는 [안드로이드 기기 원격 디버깅](https://developers.google.com/web/tools/chrome-devtools/remote-debugging)이 가능하다. [드라이버](https://www.lge.co.kr/lgekor/download-center/downloadCenterList.do#manual)를 다운로드 받고, 내 머신에 꽂아 원격 디버깅을 돌려보았다. 이런 오류가 뜬다.

![Uncaught Syntax Error on old android](/assets/images/support-old-android-on-vue.png)

### <b>Uncaught Syntax Error</b> 라니!!!

## 디버깅

1. 용의자1: [Vue.js]

    가장 먼저 의심한 것은 [Vue.js]다. 혹시 [Vue.js]가 오래된 Android 버전의 웹뷰를 지원하지 않을지도 모르니까. 그래서 검색해보니 이런 답변이 나온다. Evan You [답변](https://github.com/vuejs/vue/issues/6567#issuecomment-328549186)이다.

    > We do support all the way down to 4.4.

2. 용의자2: [axios]

    그 다음으로 의심한 것은 [axios]다. IE8에서 [axios]를 쓰기 위해서는 [promise를 폴리필 해줘야 한다](https://lovemewithoutall.github.io/it/vue-ie-support-with-es6-promise/). 하지만 그래도 안된다.

3. 용의자3: ECMAScript 2015(es6)

    그 다음으로 의심한 것은 ECMAScript 2015(es6)다. 에러 메시지를 보고 가장 먼저 생각했어야 했는데, es6에 너무 익숙해져버린 나머지 떠올리는데 시간이 걸렸다. 당연히 오래된 버전의 브라우저는 es6를 지원하지 않는다. babel을 돌렸다면 이런 일도 없었을 것이다. 늦게라도 [BABLE repl](https://babeljs.io/en/repl.html)을 돌려주었다.

## 결말

옛 JavaScript 문법으로 변환된 코드를 실행했더니 이제 잘 된다. 위의 캡쳐 화면의 에러는 `let`을 사용하여 발생한 오류였다. 당연히 es6의 화살표 함수도, Object destructring도 안되니까, 옛날 문법으로 써줘야 한다. 이렇게 문제는 해결되었다.

## 그 외

1. 이번에 알게된 것인데, Android 4.4.3 KitKat부터 웹뷰가 크게 바뀌었다. 4.4 KitKat 이전 버전에서는 크로미움으로 작성된 프로토타입의 웹뷰가 사용되었다가, Android 4.4.3 KitKat에서 Chrome 30.0.0.0 기반의 웹뷰가 사용된다고 한다([참고](https://medium.com/@pks2974/fads-9eea83f47607)).

2. 문제의 앱 사용자 중 KitKat과 그 이하 버전의 사용자는 `2.2%`였다. 아직도 사용자가 제법 많다(?).

3. Android Gingerbread에서도 [Vue.js]가 잘 돌아감을 확인했다.

## 끝!

[Vue.js]: https://vuejs.org/
[axios]: https://github.com/axios/axios
