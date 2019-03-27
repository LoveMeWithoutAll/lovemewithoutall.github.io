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

Naver 지도 Open API가 2019년 04월 15일자로 [서비스 종료](https://developers.naver.com/notice/article/10000000000030663434)된다.

해당 서비스를 계속 이용하려면 [Naver Cloud Platform]에서 새로 키를 발급받아야 한다. 달리 작업이 필요한 사항은 없고, 키만 바꿔주면 된다. 하기 예제처럼 URL의 GET 파라미터만 변경하면 된다.

```html
<!-- 구 지도 Open API -->
<script type=text/javascript src="http://openapi.map.naver.com/openapi/v3/maps.js?clientId=M7OJ2TdAfLOqiyItTfXI"></script>

<!-- NCP Map's API-->
<script type=text/javascript src="http://openapi.map.naver.com/openapi/v3/maps.js?ncpClientId=mmrqwsfb1j"></script>
```

[Naver Cloud Platform]의 Map's Enterprise API 사용 요금을 조사해보았다.

1.	Map's Enterprise API는 한시적으로 무료로 제공되는 서비스
2.	무료 이용 기간: 2019.01.01. ~ 2019.12.31.
3.	유료화 변경 1개월 전부터 메일과 SMS로 유료전환정책 및 요금제도 안내 예정
4.	상업적 목적으로 사용하는 경우 제휴 신청이 필요
5.	상업적 목적: 회사 내부 업무용이나 애플리케이션을 통해 사용자에게 비용을 받거나 광고 수익이 발생하는 경우
6.	제휴 신청: 네이버에 일 허용 사용량 제한 상향을 요청하는 절차
7.	사용 제한
    -	사용량 제한
        -	하기 기준 사용량을 초과하면 서비스 사용 불가
        -	초과 사용을 위해서는 제휴 신청 필요
        -	기준
            -	일 기준 사용량 제한: 200,000/일
            -	월 기준 사용량 제한: 6,000,000/월
    -	사용 URL 제한: 최대 10개 URL에서 사용 가능
    -	iOS, Android 앱 각 1개에서만 사용 가능
8.	참고
    -	사용제한: https://navermaps.github.io/maps.js.ncp/docs/tutorial-Quota.html
    -	운영메뉴얼: http://www.shop-websrepublic.co.kr/bbs/board.php?bo_table=manual&wr_id=103&sca=%EA%B3%B5%ED%86%B5&sst=wr_datetime&sod=asc&sop=and&page=1
    -	FAQ: https://www.ncloud.com/support/question/service

[Naver Cloud Platform]: https://www.ncloud.com/
