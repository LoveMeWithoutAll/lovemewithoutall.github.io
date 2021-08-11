---
layout: single
title: IVS Player Cannot read property ‘collectLogs’ of undefined 오류
date: 2021-08-12 00:10:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-08-10-ivs-player-bug-fix.png"
    image: "/assets/images/2021-08-10-ivs-player-bug-fix.png"
categories:
- IT
tags: [aws, ivs-player]
---

# update: 20210811

사람 환장하게 만들었던 이 오류는 ivs player 1.4.0 버전에서 fix되었다.

- Fixed an error in which “Cannot read property ‘collectLogs’ of undefined” is reported in the developer console.

https://docs.aws.amazon.com/ivs/latest/userguide/release-notes.html

-----------

## 현상
![error](/assets/images/2021-08-10-ivs-player-bug-fix.png)
amazon-ivs-player web SDK 1.3.1 version에서 발생하는 오류이다.

-----------

## 원인
“Cannot read property ‘collectLogs’ of undefined”라는 오류 메시지가 발생하는 원인은, wasm을 web server로부터 받아오지 못했기 때문에 undefined가 난 객체에서 ivs-wasmworker가 무언가 작업을 하려고 했기 때문이다. 이 문제를 해결하기 위해서는

-----------

## 해결책
web server에 allow mime type을 꼭 넣어라. 넣어야 할 type은 application/wasm

-----------

## 참고
https://forums.aws.amazon.com/thread.jspa?threadID=340175

AWS측에 거친 항의도 했다...

-----------

20210729
