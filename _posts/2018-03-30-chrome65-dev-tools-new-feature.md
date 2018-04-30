---
layout: single
title: 크롬65 버전의 개발자도구 신기능(Local overrides)
date: 2018-03-30 08:07:30.000000000 +09:00
type: post
header:
  teaser: "https://www.google.com/chrome/assets/common/images/chrome_logo_2x.png?mmfb=a5234ae3c4265f687c7fffae2760a907"
  image: "https://www.google.com/chrome/assets/common/images/chrome_logo_2x.png?mmfb=a5234ae3c4265f687c7fffae2760a907"
categories:
- IT
tags: [Chrome, Chrome dev tools, debug, frontend]
---

이거 정말 좋은건데 모르시는 분들이 있어 안타까움에 올린다.

## [Local overrides](https://developers.google.com/web/updates/2018/01/devtools#overrides)
![Local overrides](https://storage.googleapis.com/webfundamentals-assets/updates/2018/01/overrides.gif)

이제 *Local overrides* 기능을 사용하면, F5를 눌러도 개발자 도구에서 수정한 CSS와 문서 내용이 날아가지 않는다. 여기서 수정한 정적 소스들은 *Sources > Overrides* 에서 지정한 폴더에 저장된다. 

### 보충
[chrome66 부터는 HTML 파일 안에 정의된 styles도 저장이 된다.](https://developers.google.com/web/updates/2018/02/devtools#overrides)

## [Changes tab](https://developers.google.com/web/updates/2018/01/devtools#changes)
![Changes tab](https://developers.google.com/web/updates/images/2018/01/changes.png)

와 함께 쓰면 내가 뭘 바꿨는지 알 수 있어 더 좋다.

개발자 도구에서 CSS 등을 변경하고, 완성된 소스를 *Changes tab*에서 확인한 후, 로컬에 저장된 파일을 배포하면 편리하다.

링크의 개발자 도구 소개 영상도 꼭 보자.