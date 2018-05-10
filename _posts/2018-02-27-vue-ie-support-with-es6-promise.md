---
layout: single
title: IE에서 Vue.js 사용하기
date: 2018-02-27 08:07:30.000000000 +09:00
type: post
header:
  teaser: "/assets/images/old_ie.jpg"
  image: "/assets/images/old_ie.jpg"
categories:
- IT
tags: [Vue.js, IE, axios, es6-promise]
---

## 오류
[Vue.js]에서 ajax요청을 할 때는 [axios]를 사용하기 마련이다. 편리하고 좋은 라이브러리지만, 구형 IE에서 실행하면 이런 오류가 뜬다.

```javascript
ReferenceError: 'Promise'이(가) 정의되지 않았습니다.
   {
      [functions]: ,
      description: "'Promise'이(가) 정의되지 않았습니다.",
      message: "'Promise'이(가) 정의되지 않았습니다.",
      name: "ReferenceError",
      number: -2146823279
   }
```
[axios]가 IE를 지원하지 않기 때문이다. 이는 [Vuex]도 마찬가지다.

## 해결책
[es6-promise](https://github.com/stefanpenner/es6-promise)를 적용하면 된다.

```html
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/es6-promise@4/dist/es6-promise.min.js"></script>
<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/es6-promise@4/dist/es6-promise.auto.min.js"></script> 
```

위 스크립트를 넣으면 이제 IE에서도 [axios]를 이용한 비동기 http 통신이 잘 된다.

[webpack]을 사용하는 경우에는 아래와 같이 하면 된다.

먼저 설치를 하고

```bash
# 설치
npm install es6-promise --save
```

entry로 사용하는 javascript 파일에 다음과 같이 넣어주면, 전역 환경(global environment)에 적용된다.
다른 파일에 달리 import하거나 require를 적어줄 필요가 없다. 둘 중 하나를 선택해서 넣어주자.

```javascript
// CommonJs 방식
require('es6-promise').polyfill();
```

```javascript
// ES6 방식
import 'es6-promise/auto'
```

이래도 여전히 *'Promise'이(가) 정의되지 않았습니다.* 오류가 뜬다면, [es6-promise]를 불러오는 순서를 위로 땡겨주자. 적어도 [Vuex]나 [axios]를 사용하는 시점보다는 빨리 불러와야 정상 동작한다.

[Vue.js]: https://vuejs.org
[axios]: https://github.com/axios/axios
[webpack]: https://webpack.js.org/
[Vuex]: https://vuex.vuejs.org/kr/