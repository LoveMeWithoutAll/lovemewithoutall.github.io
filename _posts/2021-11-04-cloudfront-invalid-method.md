---
layout: single
title: CloudFront에 HTTP request를 보낼 때, 존재하지 않는 method(HTTP Verb)로 보내면 어떻게 될까?
date: 2021-11-02 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png"
    image: "https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png"
categories:
- IT
tags: [aws]
---

CloudFront(이하 CF)에 HTTP request를 보낼 때, 존재하지 않는 method(HTTP Verb)로 보내면 어떻게 될까?

일반적으로 CF에 request가 들어오면
1. TCP 맺어지고
2. TLS 통과하고
3. CloudFront distribution을 찾아가는데

존재하지 않는 method라면 3단계까지 가지 않는다. 3단계를 가지 못하니 error page redirection rule은 동작하지 않는다. 대신 CF에서는 CF에 default로 설정된 400 error html(사용자 커스터마이징 불가)을 response 해준다. 

20211015
