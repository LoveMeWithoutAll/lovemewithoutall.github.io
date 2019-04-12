---
layout: single
title: Firebase Cloud Firestore 데이터베이스에 안전하지 않은 규칙이 있습니다
date: 2019-04-12 13:19:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [firebase]
---

# 현상

벌써 1달은 된 일이다. Google Firebase에서 이런 메일이 날아왔다.

![firestore-security-alert](/assets/images/firestore-security-alert.png)

[내 블로그](https://book-blog-with-largo.firebaseapp.com/)가 위기에 처했다고 한다.

# 원인

[Cloud Firestore]의 보안 규칙을 <b><u>대충</u></b> 설정했기 때문이다.

아래 코드는 내가 설정해놓은 보안 규칙이다. 보안 취약점은 다음과 같다.

1. 모든 문서 경로에 접근이 가능하다. 하기 코드 3번째 line 참조.
1. read 권한이 무조건 allow다. 하기 코드 4번째 line 참조.

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read;
      allow write: if request.auth != null;
    }
  }
}
```

# 조치

1. Post 문서에만 접근이 가능하도록 했다. 하기 코드 3번째 line 참조.
2. show == true인 문서만 read를 allow 할 수 있도록 권한을 부여했다. 하기 코드 4번째 line 참조.

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /Post/{document=**} {
      allow read: if resource.data.show == true;
      allow write: if request.auth != null;
    }
  }
}
```

본 블로그의 [소스 코드](https://github.com/LoveMeWithoutAll/book-blog/blob/master/firestore.rules)에서 확인 가능하다.

# 끝

이제 보안 알림 메일이 더이상 오지 않는다. [Cloud Firestore] 초보자로서 벌인 황당한 실수다. 부끄러워서 포스팅도 안하려고 했는데, 혹시나 누군가에게 도움이 될까 싶어 조심스럽게 올려본다. 부디 저같은 실수하지 않으시길!

## 참고
https://www.fullstackfirebase.com/cloud-firestore/security-rules
https://firebase.google.com/docs/firestore/security/rules-query

[Cloud Firestore]: https://firebase.google.com/docs/firestore/?hl=ko