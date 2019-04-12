---
layout: single
title: [Firebase] Cloud Firestore 데이터베이스에 안전하지 않은 규칙이 있습니다
date: 2014-04-12 13:19:30.000000000 +09:00
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

<div style="font-family:roboto, sans-serif;border:1px solid #E0E0E0;background-color:white;max-width:600px;margin:0 auto"><div style="background-color:white;padding:24px 0"><img style="margin:auto;display:block;height:40px;max-height:40px;min-height:40px" src="https://firebase.google.com/_static/053546c383/images/firebase/lockup.png"></div><table style="width:100%;background-color:#039BE5" cellpadding="0" cellspacing="0"><tr><td style="padding:24px"><div style="font-size:13px;line-height:16px;color:#B3E1F7">book-blog</div><div style="font-size:20px;line-height:24px;color:white">[Firebase] Cloud Firestore 데이터베이스에 안전하지 않은 규칙이 있습니다</div></td><td style="padding:24px;text-align:right"><img style="height:24px;width:24px" src="https://www.gstatic.com/mobilesdk/180621_mobilesdk/firebase_database_white_24@2x.png"></td><tr></table><div style="margin-bottom:24px;padding:24px 24px 0 24px"><div style="margin-bottom:24px"><div style="background-color:#FFF3E0;padding:16px 24px"><table style="width:100%" cellpadding="0" cellspacing="0"><tr><td style="width:40px;vertical-align:top"><img src="https://www.gstatic.com/mobilesdk/171101_mobilesdk/images/caution_orange_24dp_@2x.png" style="height:24px;width:24px;margin-right:16px;vertical-align:middle"></td><td><span style="color:#E65100;font-size:14px;font-weight:500;line-height:20px">보안 규칙에서 다음 문제가 감지되었습니다.<ul style="margin-top:4px;margin-bottom:0"><li style="color:#E65100;font-size:14px;font-weight:500">모든 사용자가 전체 데이터베이스를 읽을 수 있습니다.</li></ul></span></td></tr></table></div></div><div style="font-weight:400;font-size:14px;line-height:20px;color:#212121">프로젝트에 강력한 보안 규칙이 없으므로 누구나 전체 데이터베이스에 액세스할 수 있습니다. 공격자가 데이터를 도용, 수정, 삭제할 수 있으며, 이로 인해 청구 금액이 급증할 수 있습니다.</div></div><div style="background-color:#E0E0E0;height:1px;width:100%"></div><div style="margin-bottom:24px;padding:24px 24px 0 24px"><div style="margin-bottom:16px"><div style="font-family:roboto, sans-serif;font-weight:500;font-size:12px;line-height:16px;color:#757575;text-transform:uppercase">안전하지 않은 규칙</div></div><div style="font-weight:500;font-size:20px;line-height:24px;color:#212121;margin-bottom:16px">book-blog</div><div style="margin-bottom:16px"><div style="margin-bottom:16px"><div style="font-weight:400;font-size:14px;line-height:20px;color:#757575">데이터베이스</div><div style="font-weight:400;font-size:14px;line-height:20px;color:#212121">(default)</div></div></div><div style="padding:24px 0 0 0;line-height:16px;text-align:right"><div style="margin:0 0 16px 0"><span style="margin:0 0 0 8px"><a href="https://firebase.google.com/docs/firestore/security/insecure-rules" style="font-size:14px;font-weight:500;letter-spacing:0.25px;text-decoration:none;text-transform:none;display:inline-block;border-radius:8px;padding:9px 19px;border:1px solid #E0E0E0;color:#039BE5">자세히 알아보기</a></span><span style="margin:0 0 0 8px"><a href="https://console.firebase.google.com/project/book-blog-with-largo/database/firestore/rules" style="font-size:14px;font-weight:500;letter-spacing:0.25px;text-decoration:none;text-transform:none;display:inline-block;border-radius:8px;padding:10px 20px;background-color:#039BE5;color:#FFFFFF">규칙 수정</a></span></div></div></div><div style="background-color:#E0E0E0;height:1px;width:100%"></div><div style="margin-bottom:24px;padding:24px 24px 0 24px"><div style="font-size:14px;line-height:20px;color:#212121;font-weight:400"><div>궁금하신 점이 있거나 본 이메일이 잘못 전송되었다면 <a style="text-decoration:none;color:#039BE5" href="https://firebase.google.com/support/">Firebase 지원팀</a>에 문의하시기 바랍니다.</div><div style="margin-top:24px">Firebase를 사용해 주셔서 감사합니다.</div></div></div><div style="background-color:#ECEFF1;padding:24px;font-size:12px;line-height:16px"><div style="color:#757575;text-align:center">Firebase 프로젝트와 관련하여 중요한 서비스 정보를 알려드리기 위해 발송된 이메일입니다.</div><div style="margin-top:20px"><div style="color:#757575;text-align:center"><a href="https://console.firebase.google.com/subscriptions/project/book-blog-with-largo" style="text-decoration:none;color:#039BE5">알림 설정</a> 관리</div></div></div><div style="background-color:#78909C;padding:24px"><table style="width:100%" cellpadding="0" cellspacing="0"><tr><td><img style="height:24px;max-height:24px;min-height:24px" src="https://www.gstatic.com/images/branding/googlelogo/2x/googlelogo_light_color_74x24dp.png"></td><td><div style="font-size:10px;line-height:14px;font-weight:400;text-align:right"><a style="color:#D6DDE1;text-decoration:none">Google LLC<br>1600 Amphitheatre Pkwy<br>Mountain View, CA, 94043 USA</a></div></td></tr></table></div></div>

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