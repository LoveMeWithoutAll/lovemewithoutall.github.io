---
layout: single
title: AWS CloudFront에서 S3의 index.html을 찾지 못하는 현상
date: 2021-10-18 23:23:30.000000000 +09:00
type: post
header:
    teaser: "https://www.pngfind.com/pngs/m/56-565278_amazon-cloudfront-logo-png-amazon-cloudfront-logo-transparent.png"
    image: "https://www.pngfind.com/pngs/m/56-565278_amazon-cloudfront-logo-png-amazon-cloudfront-logo-transparent.png"
categories:
- IT
tags: [aws]
---

## 현상
CloudFront -> S3로 index.html 파일을 요청했는데 이런 오류가 발생
![confusing](/assets/images/2021-10-18-cloud-front-s3-index.png)

## 원인
CloudFront -> S3로 request를 전달할 때, HTTP request의 url(dev-live11-view.11st.co.kr)로 host header를 전달했음. 하지만 해당 host로는 S3의 bucket 주소를 찾을 수 없음(없으니까)

## 해결책
CloudFront -> S3로 request를 전달할 때, host header를 빼고 전달하라.
물론 CloudFront -> S3로의 request 전달 설정은 사전에 잘 되어 있어야 한다.

20210902
