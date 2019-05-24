---
layout: single
title: Vue CLI 패키지 매니저 변경하기
date: 2019-05-24 18:07:30.000000000 +09:00
type: post
header:
    teaser: "https://cli.vuejs.org/favicon.png"
    image: "https://cli.vuejs.org/favicon.png"
categories:
- IT
tags: [Vue.js, JavaScript]
---

[Vue CLI]를 사용하다 보면 이런 에러가 발생할 때가 있다.

```
error 404 Not Found - GET https://cdn.npm.taobao.org/electron-to-chromium/-/electron-to-chromium-1.3.137.tgz
error 404
error 404 'electron-to-chromium@^1.3.133' is not in the npm registry.
error 404 You should bug the author to publish it (or use the name yourself!)
```

NPM의 Mirror인 [Taobao Registry]가 깨져서 발생하는 오류이다. (NPM의 registiry는 느려터졌기 때문에, 보통은 [Taobao Registry]를 사용한다.).

[Taobao Registry]가 복구되기를 기다릴 시간이 없으니, [Vue CLI]의 패키지 매니저를 바꾸자. NPM을 사용하고 있다면 [yarn]으로, 혹은 그 반대로 바꿔도 된다.

먼저 [Vue CLI]의 설정 파일을 확인하자. 콘솔에 아래 커맨드를 입력하면,

```cmd
vue config
```

아래와 같이 출력된다. Resolved path가 [Vue CLI] 설정 파일의 경로다. 해당 파일의 **packageManager** 항목을 수정하면 된다. 아래 예제에서는 *npm*을 *yarn*으로 변경했다.

```javascript
// OS는 Windows10 기준이다
Resolved path: C:\Users\ys\.vuerc
 {
  "useTaobaoRegistry": true,
  "packageManager": "yarn", // 여기를 수정한다
  "presets": {
    "client": {
      "useConfigFiles": false,
      "plugins": {
        "@vue/cli-plugin-babel": {},
        "@vue/cli-plugin-eslint": {
          "config": "standard",
          "lintOn": [
            "save"
          ]
        }
      },
      "router": true,
      "vuex": true
    }
  }
}
```

끝!

[Vue CLI]: https://cli.vuejs.org/
[Taobao Registry]: https://npm.taobao.org/
[yarn]: https://yarnpkg.com/lang/en/