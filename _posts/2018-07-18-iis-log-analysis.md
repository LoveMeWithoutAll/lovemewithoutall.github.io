---
layout: single
title: IIS Log 분석
date: 2018-07-16 17:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/iisstart.png"
    image: "/assets/images/iisstart.png"
categories:
- IT
tags: [IIS, log]
---

# 가내수공업적 로그 분석

서비스를 운영하다보면 로그를 분석해야 할 때가 많다. 내가 운영하는 서비스 중에 [IIS](https://msdn.microsoft.com/ko-kr/library/hh831725%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396)를 사용하는 서비스가 많은데, 왜 그런지는 모르겠지만 웹서버를 위한 실시간 로그 분석 체계가 구축되어 있지 않았다. [GoAccess](https://goaccess.io/) 같은걸 구축하면 좋겠지만, 내게는 이를 결정할 권한도 자원도 없다. 그런거 없어도 잘 돌아간다는 생각이겠지만... 아무튼 가끔은 로그를 분석할 일이 생긴다. 가내수공업 스타일로 로그 분석을 해보았다.

# 환경

## Server
* Windows Server 2008 R2
* IIS 7.5
* Log type: IISW3CLOG

## 내 머신

* Windows 10

# Log Parser

1. [Log Parser 2.2](https://www.microsoft.com/en-us/download/confirmation.aspx?id=24659)를 설치한다.

1. 스크립트를 실행한다.

1. 하기 스크립트는 *uri*에 *aspx*가 포함된 http 요청을 group by로 count하는 요청이다. SQL 문법과 거의 동일하다.

```cmd
logparser -i:W3C -o:csv "SELECT cs-uri-stem, COUNT(*) AS Hits into d:\results.csv FROM d:\u_ex180612.log WHERE cs-uri-stem like '%.aspx%' GROUP BY cs-uri-stem ORDER BY Hits DESC"
```

# Log Parser Studio

LogParser를 편리하게 실행할 수 있도록 도와주는 어플리케이션이 있다. 바로 [Log Parser Studio](https://gallery.technet.microsoft.com/Log-Parser-Studio-cd458765)인데, 나온지는 꽤 됐지만 가내수공업 용도로는 부족함이 없다. 다운받은 후 압축을 풀면 들어있는 *README.txt* 파일에 사용법이 잘 나와있다. 하긴 굳이 사용법 설명이 필요없을 정도로 깔끔하게 만들어져 있다.

끝!