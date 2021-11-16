---
layout: single
title: AWS CloudFront error page 설정에서 주의할 점
date: 2021-11-16 21:00:30.000000000 +09:00
type: post
header:
    teaser: "[/assets/images/2021-11-11-build-artifact-source-artifact.png](https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png)"
    image: "[/assets/images/2021-11-11-build-artifact-source-artifact.png](https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png)"
categories:
- IT
tags: [aws]
---

CloudFront(이하 CF)에서 error page redirection은 1회의 request에서 1회만 가능하다. 2회 이상 동작하지 않는다. 예를 들어보자.

1. 400 error가 발생하여 '/404' path로 redirection
2. '/404' path에 리소스가 없으니 '/index.html/404로 redirection

위 2차례의 redirection은 동작하지 않는다.

CF에서는 왜 이런 동작을 막아놨을까? 내가 일하는 프로젝트의 AWS 담당자도 그 이유를 명확하게 알지는 못했다. 내 추측으로는 redirection간 상호 순환 참조가 발생할까봐 막아둔 게 아닐까 한다.

react, vue.js 같은 프론트엔드 프레임워크에서는 예시의 2를 거의 항상 사용하니 CF error page rule 설정에 주의해야 한다.

20211014
