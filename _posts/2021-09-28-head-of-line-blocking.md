---
layout: single
title: HOL(Head of line blocking)
date: 2021-09-28 02:16:30.000000000 +09:00
type: post
header:
    teaser: "https://miro.medium.com/max/2233/0*DvW52URHvIUtt9i7"
    image: "https://miro.medium.com/max/2233/0*DvW52URHvIUtt9i7"
categories:
- IT
tags: [network]
---

HOL(head of line): TCP에서의 전송 지연으로 인한 레이턴시

1. 원인: TCP의 전송 순서는 엄격히 정해져 있다. 만약 먼저 전송되어야 할 TCP 패킷 하나가 소실되면 그 패킷이 재전송 될 때까지 다른 모든 패킷이 블로킹된다

2. HTTP/1.1에서: HTTP pipelining(클라이언트에서 요청 큐를 쌓는 게 아니라, 서버에서 응답 큐를 쌓는 것. 클라이언트에서 요청을 여러 개 동시에 보낸다. 서버에서는 받은 요청을 처리한다.)에서 데이터 전송의 한계 발생. 선 요청 받은 건을 다 전송하지 못하면, 후 전송 건도 늦춰진다. 서버의 버퍼에는 후 전송 건이 쌓여 리소스가 소모된다. 때문에 서버 공격에 악용될 수 있다. 만약 응답이 실패하면 이후 모든 요청도 다시 보내야 한다. => HTTP pipelining은 실패한 기술이 되었다

3. HTTP/2.0에서: 없어짐. 요청과 응답 멀티플렉싱이 가능해진 덕분이다. 하나의 tcp연결에서 다수의 요청과 응답을 병렬로 주고받을 수 있게 되었기에 아무 것도 블로킹하지 않는다.

20210928
