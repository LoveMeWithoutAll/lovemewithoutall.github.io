---
layout: single
title: iOS, Android 웹뷰 하위 호환성 지원 문제
date: 2024-06-28 09:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend]
---

하위 호환성은 늘 고민스러운 문제다. 웹뷰에서도 그렇다. JS의 새 버전이 나오면서 웹뷰가 JS의 신기능을 지원하지 않는 경우가 발생했다. iOS와 Android의 버전이 올라가면서 이 문제는 해결되었지만, 세상에는 여전히 구형 기기를 사용하는 사람들이 많다. 이 분들은 이상하게도 숫자는 적지만 막대한 매출을 일으키는 경향(?)이 있는지라 결코 가볍게 볼 수 없다. 그래서 웹뷰의 하위 호환성을 어디까지 지원해야 하는지, 11키티즈 개발 경험을 들어 간단히 정리해보았다.

1. esbuild
    1. 사유: esbuild는 es2015부터 지원한다. es2015는 화살표 함수와 let 등이 도입된 대격변의 버전이지만, Android chrome은 2017년에서조차도 es2015를 완전히 지원하지 못했다. 반면 의외로 safari는 es2015를 일찌감치 100% 지원했다. 이런 제약사항이 싫다면 전통의 babel을 써야한다.
    2. Support version
        1. Android Chrome: 안정적으로 지원하기 위해 v69를 사용
        2. iOS Safari: 적당히
    3. 참고
        1. 2017년과 이후 JavaScript의 동향 - JavaScript(ECMAScript) https://d2.naver.com/helloworld/2809766
        2. es5는 지원하지 않겠따는 esbuild의 공식 입장: https://github.com/evanw/esbuild/issues/297#issuecomment-1670072906
2. PWA
    1. 사유: PWA를 사용하려면 globalThis를 지원해야 한다. globalThis는 단순한 window 전역 객체가 아니다. 그래서 폴리필을 직접 만들기 상당히 까다롭다(https://ui.toast.com/weekly-pick/ko_20190503 참조). 폴리필을 사용하든가, 아니면 하위 호환성을 일부 포기해야 한다.
    2. Support version
        1. Chrome Android: 65
        2. iOS: 12.2
    3. 참고: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis
3. JS module 문법
    1. 사유: module import는 사실상 포기할 수 없는 문법이다.
    2. Support version
        1. Android Chrome: v61부터는 지원이 되지만, 백화현상이 발생하는 등 불안정한 모습을 보여 v64부터 사용하는 것을 사용했다.
        2. iOS: 마음껏 써도 된다.
    3. 참고: https://babeljs.io/docs/learn/#modules

20240628
