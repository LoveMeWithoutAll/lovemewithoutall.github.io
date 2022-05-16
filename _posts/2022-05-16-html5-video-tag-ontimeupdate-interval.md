---
layout: single
title: HTML video tag onTimeUpdate 동작 주기
date: 2022-05-16 12:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2022-05-16-html5-video-tag-ontimeupdate-interval.png"
    image: "/assets/images/2022-05-16-html5-video-tag-ontimeupdate-interval.png"
categories:
- IT
tags: [HTML5]
---

HTML video tag의 onTimeUpdate 프로퍼티에 박히는 콜백은 가장 느릴 경우 250ms 마다 1회 동작한다.

테스트 해보니 영상을 그냥 재생하게 두는 경우, 1초에 약 4번 onTimeUpdate 콜백이 호출된다.

HTML5 spec 문서의 관련 부분은 이렇다.

![html5 spec](/assets/images/2022-05-16-html5-video-tag-ontimeupdate-interval.png)

### 참고

- https://html.spec.whatwg.org/#event-media-timeupdate
- https://stackoverflow.com/a/12325960/8403199

20220413
