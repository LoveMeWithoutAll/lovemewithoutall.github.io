---
layout: single
title: HTML Video element를 재생하려고 했더니 영상이 나오다가 만다. safari 버그가 아닐까? webkit 소스코드를 까보았다
date: 2025-08-26 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://support.apple.com/content/dam/edam/applecare/images/en_US/psp/psp_heroes/mini-hero-safari.png"
    image: "https://support.apple.com/content/dam/edam/applecare/images/en_US/psp/psp_heroes/mini-hero-safari.png"
categories:
- IT
tags: [safari]
---

## 1줄 요약

버그 아님

## 환경

iOS safari (버전 무관)

## 현상

iOS safari 브라우저에서 사용자 제스처 없이 unmute 상태의 video element를 프로그래밍적으로 재생을 시도했을 때(`videoElement.play()`), 

<b>video가 순간적으로 재생되려다가 취소되면서 `NotAllowedError`를 뱉는 경우가 있다.</b>

영상 재생을 막을 것이면 처음부터 막을 것이지, 왜 영상 재생을 시작했다가 취소시키는 것일까? 내가 뭔가 잘못한 것은 아닐까? 사용자 제스처 없이도 video autoplay가 가능한 방법이 있는 것은 아닐까?

라는 희망을 품으며 webkit의 소스코드를 분석해 보았다.

## 원인

webkit 2.5 기준, HTMLMediaElement의 소스코드를 살펴보면

https://github.com/WebKit/WebKit/blob/webkitglib/2.50/Source/WebCore/html/HTMLMediaElement.cpp#L4355

video element를 프로그래밍적으로 `play()`할 경우, 

현재 재생 중이더라도 세션이 ‘playbackPermitted()’를 통과 못 하면, `play()` 프라미스를 `NotAllowedError`로 거절한다.

## 결론

HTML video 자동 재생(autoplay)에는 사용자 제스처(user gesture)가 반드시 필요하다.

헛된 희망을 버려라.

EOD

20250826
