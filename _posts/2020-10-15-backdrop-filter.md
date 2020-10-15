---
layout: single
title: backdrop-filter로 모달 뒤를 날리자
date: 2020-10-15 08:23:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2020-10-15-backdrop-filter/cms.inha.ac.kr_generaledu_clinic_.png"
    image: "/assets/images/2020-10-15-backdrop-filter/cms.inha.ac.kr_generaledu_clinic_.png"
categories:
- IT
tags: [JavaScript, CSS]
---

모달을 띄울 때, 뒤 배경을 흐릿하게 만들면 모달의 가독성이 향상된다. `backdrop-filter` CSS를 쓰면 이걸 쉽게 만들 수 있다.

![backdrop-filter](/assets/images/2020-10-15-backdrop-filter/cms.inha.ac.kr_generaledu_clinic_.png)

우선 전체 코드는 다음과 같다.

```html
<html>
  <body>
    <div>본문</div>
    <div class="modalBackground">
      <div class="modal">모달</div>
    </div>
  <body>
  <style>
    .modalBackground {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      backdrop-filter: blur(5px);
    }
    .modal {
      width: 50%;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%) scale(1);
    }
  </style>
</html>
```

이제 하나하나 살펴보자.

`<div>본문</div>`는 class `modalBackground`에 의해 가려질 `div`다.

class `modal`은 화면에 띄워주려는 모달이다. `transform: translate(-50%, -50%) scale(1);`으로 화면 가운데에 예쁘게 박아줬다.

class `modalBackground`는 화면 전체를 가린다. 위 코드에서는 `backdrop-filter: blur(5px);`로 가려진 화면을 blur 처리했다.

Chrome developer tools의 layout 탭을 보면 이해하기 쉽다. 푸른색의 `modalBackground`가 뒤의 본문을 다 가리고 있는 것!

![layout](/assets/images/2020-10-15-backdrop-filter/layout.png)
