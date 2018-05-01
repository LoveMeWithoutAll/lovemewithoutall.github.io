---
layout: single
title: SurveyJS의 visibleIf 속성
date: 2018-05-01 14:00:30.000000000 +09:00
type: post
header:
    teaser: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
    image: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
categories:
- IT
tags: [SurveyJS]
---

[SurveyJS]로 설문조사 서비스를 개발하다가 발견한 것을 공유한다.

[Survey Builder](https://surveyjs.io/Survey/Builder/)로 JSON을 만들다보면, 각 질문마다 **visibleIf**라는 속성이 있다. "true"면 화면에 해당 질문이 보이고, "false"면 보이지 않는다.

### boolean 타입의 true/false가 아니다.
### string 타입의 "true"/"false"다.

따라서 아래와 같이 JSON을 작성해줘야 한다.

```json
{
    // 생략
    "type": "checkbox",
    "name": "Q1",
    "title": "test quesition",
    "isRequired": true,
    "visibleIf": "false", // "false"이기에 이 질문은 보이지 않는다
    "choices": [
        "item1",
        "item2",
        "item3"
    ],
    // 생략
}
```

[SurveyJS]: https://surveyjs.io/