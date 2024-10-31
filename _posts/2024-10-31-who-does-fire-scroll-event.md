---
layout: single
title: scroll event는 어디에서 발생하는가?
date: 2024-10-31 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [html]
---

HTML element의 scroll 이벤트는 어디에서 발생하는가? 햇갈리는 질문이다. 다시 말해보자. 내가 일으킨 scroll 이벤트는 어느 element에서 잡아 채는가? 그래도 햇갈린다. 다시 말해보자. onscroll 이벤트를 어느 element에 설정해야지 내가 원하는대로 scroll 이벤트를 발생시킬 수 있는가?

브라우저 개발자 도구의 element 탭에 답이 있다.

## Google Chrome browser

구글 크롬 브라우저의 element 탭에서는 `scroll` 이라고 표시를 해준다. `scroll` 표시가 붙은 element에서만 onscroll 이벤트가 동작한다.

![chrome devtools element tab](/assets/images/2024-10-31-who-does-fire-scroll-event/chrome.png)

chrome version 130

## Safari browser

사파리 브라우저에서도 마찬가지다.

![safari element tab](/assets/images/2024-10-31-who-does-fire-scroll-event/safari-scroll-only.png)

오히려 더 좋다. 사파리는 element에 `event`가 붙어있다면 추가로 표시를 해준다. 크롬 브라우저에는 없는 기능이다.

![safari element tab with scroll](/assets/images/2024-10-31-who-does-fire-scroll-event/safari-scroll-with-event.png)

`Event 마크`를 클릭하면 추가 정보도 보여준다. chrome에서는 번거로운 이벤트 디버깅을 하기도 쉽다.

![safari event info](/assets/images/2024-10-31-who-does-fire-scroll-event/safari-event-info.png)

safari version 18.1

## Firefox

파이어폭스 브라우저는 더더더 좋다. scroll 이벤트에 부착된 함수의 코드까지 한 눈에 볼 수 있다. 아래 캡쳐에서는 `react-dom`의 `dispatchContinuousEvent` 함수를 보여준다. 과연 개발자 도구의 근본이라 할 수 있다.

![firefox](/assets/images/2024-10-31-who-does-fire-scroll-event/firefox.png)

firefox version 132

### 디버깅이 막히면 다른 브라우저의 개발자 도구를 돌려보면 좋다.

20241031
