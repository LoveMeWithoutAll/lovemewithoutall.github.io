---
layout: single
title: AWS CodeDeploy build artifact 남기기
date: 2021-11-18 20:35:30.000000000 +09:00
type: post
header:
    teaser: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
    image: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
categories:
- IT
tags: [aws]
---

'**/*' 로 설정해야 하위 디렉토리의 파일까지 재귀적으로 artifact에 포함된다. AWS 문서에도 나와 있기는 한데... 다소 애매하게 설명하고 있어서 놓치기 쉽다.

create-react-app의 기본 설정을 사용한다면, build 디렉토리 아래에 build output이 나온다. 이 경우에는 아래와 같이 설정하면 된다.

```
artifacts:
  files:
    - ./build/**/*
  discard-paths: no
```

20211022
