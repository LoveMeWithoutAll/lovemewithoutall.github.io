---
layout: single
title: Naver Cloud Platform Map's API
date: 2019-03-27 11:32:30.000000000 +09:00
type: post
header:
    teaser: "https://www.ncloud.com/public/img/logo.png"
    image: "https://www.ncloud.com/public/img/logo.png"
categories:
- IT
tags: [Web]
---

The Naver Maps Open API will be [discontinued](https://developers.naver.com/notice/article/10000000000030663434) as of April 15, 2019.

To keep using the service, you need to get a new key issued from the [Naver Cloud Platform]. No other work is required—you just need to swap the key. As in the example below, all you have to do is change the GET parameter in the URL.

```html
<!-- Old Maps Open API -->
<script type=text/javascript src="http://openapi.map.naver.com/openapi/v3/maps.js?clientId=M7OJ2TdAfLOqiyItTfXI"></script>

<!-- NCP Map's API-->
<script type=text/javascript src="http://openapi.map.naver.com/openapi/v3/maps.js?ncpClientId=mmrqwsfb1j"></script>
```

I looked into the pricing for the Map's Enterprise API on the [Naver Cloud Platform].

1.	The Map's Enterprise API is a service offered free of charge for a limited time
2.	Free usage period: 2019.01.01. ~ 2019.12.31.
3.	Notice about the policy for switching to a paid model and the pricing plans will be sent via email and SMS starting one month before the switch to a paid model
4.	If you use it for commercial purposes, a partnership application is required
5.	Commercial purpose: when it is used for a company's internal business, or when you charge users a fee or generate advertising revenue through an application
6.	Partnership application: the procedure for requesting that Naver raise the daily usage limit
7.	Usage limits
    -	Usage volume limit
        -	The service cannot be used once you exceed the usage limits below
        -	A partnership application is required to use it beyond the limit
        -	Limits
            -	Daily usage limit: 200,000/day
            -	Monthly usage limit: 6,000,000/month
    -	URL usage limit: usable on up to 10 URLs
    -	Usable on only 1 iOS app and 1 Android app each
8.	References
    -	Usage limits: https://navermaps.github.io/maps.js.ncp/docs/tutorial-Quota.html
    -	Operations manual: http://www.shop-websrepublic.co.kr/bbs/board.php?bo_table=manual&wr_id=103&sca=%EA%B3%B5%ED%86%B5&sst=wr_datetime&sod=asc&sop=and&page=1
    -	FAQ: https://www.ncloud.com/support/question/service

[Naver Cloud Platform]: https://www.ncloud.com/
