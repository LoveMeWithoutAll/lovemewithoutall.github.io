---
layout: single
title: javascript 조건부 객체 프로퍼티
date: 2022-03-16 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2022-03-16-typescript-conditional-object-property/example.png"
    image: "/assets/images/2022-03-16-typescript-conditional-object-property/example.png"
categories:
- IT
tags: [javascript]
---


객체에 조건부로 프로퍼티를 추가할 때 사용하는 방법.

```javascript
const obj = {
    ...(isTrue && {a: 'a'})
}

// isTrue가 true면
obj = {a: 'a'}

// isTrue가 false면
obj = {}
```

![example](/assets/images/2022-03-16-typescript-conditional-object-property/example.png)

유용하게 사용하고 있는 패턴인데, 코드 리뷰 중에 훌륭한 팀원이 이런 걸 발견했다.
1. boolean과 object destructing을 함께 쓰면 문제 없는데(일반적인 케이스)
2. false를 쓰면 아래와 같이 오류가 난다. object type이 아니라 spread 연산자를 쓸 수 없다는 오류.

![error](/assets/images/2022-03-16-typescript-conditional-object-property/error.png)

추측컨대, typescript 내부적으로 boolean과 false를 다르게 처리하는 것 같다. 버그를 유발할 수 있는 이런 사소한(?) 것들까지 컴파일 전에 미리 알려준다니, typescript는 참으로 지혜로운(?) 언어가 맞다.

그 외, 이걸 파해쳐보려고 typescript git repo를 처음 들어가봤는데, 좌절감만 안고 돌아섰다....

20220224
