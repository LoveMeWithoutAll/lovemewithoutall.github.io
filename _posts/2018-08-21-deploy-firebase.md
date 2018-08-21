---
layout: single
title: Deploy Vue SPA on firebase 
date: 2018-08-17 09:45:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [firebase]
---

[firebase cli]로 [firebase hosting]에 [Vue.js]로 만든 SPA를 배포하는 순서를 정리해보았다.

# 1. firebase cli 설치

```bash
npm install -g firebase-tools
```

# 2. firebase login

```bash
firebase login
```

# 3. firebase init

```bash
# cd project-directory
firebase init
```

# 4. init setting

```bash
# firebase cli가 시키는대로 누른다
```

# 5. firebase.json 설정

firebase.hosting.public 의 기본 설정은 *public*이다. *npm run build*의 output이 떨궈지는 *dist* 폴더로 변경하자.

```json
{
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json"
  },
  "hosting": {
    "public": "./dist", // 이 부분을 바꿔준다
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  },
  "storage": {
    "rules": "storage.rules"
  }
}

```

# 6. npm run build

```bash
npm run build
```

# 7. firebase deploy

이제 *dist* 폴더의 output을 firebase hosting에 올리자.
```bash
firebase deploy
```

# 8. 마무리

이제부터는 배포할 때 **6** , **7**번 과정만 하면 된다.

# 끝

[firebase cli]: https://firebase.google.com/docs/cli/?hl=ko
[firebase hosting]: https://firebase.google.com/docs/hosting/deploying?hl=ko
[Vue.js]: https://vuejs.org/