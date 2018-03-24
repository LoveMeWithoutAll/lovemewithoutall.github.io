---
layout: single
title: 라임서베이 설문조사 서버 설치하기
date: 2017-11-17 17:07:30.000000000 +09:00
type: post
header:
  teaser: "https://demo.limesurvey.org/tmp/assets/cf96b0ea/logo.png"
  image: "https://demo.limesurvey.org/tmp/assets/cf96b0ea/logo.png"
categories:
- IT
tags: [survey, self hosted survey, LimeSurvey, self host]
---
# 0. 시작

나는 직장에서 가끔씩 묘한 요구를 받고는 하는데, 자기네 부서를 위한 전용 설문조사 서비스를 구축해달라는 것이다. [구글 설문조사]는 개인정보 노출 위협 어쩌구 하면서 쓰기 싫고, 이미 제공하고 있는 범용 설문조사 서비스는 사용하고 싶지 않다고 한다. 물론 나름의 이유야 있겠지만... 아무튼 이런 일이 있을 때마다 별다른 대꾸없이 매번 노가다성 작업으로 웹페이지를 만들어준다. 헌데 오늘따라 나도 심통이 좀 났다. 그래서 안하던 짓을 해보았다. 기존의 설문조사 서비스는 쓰기 싫고, [구글 설문조사]도 쓰기 싫다고? 그럼 자체 구축 서버에서 돌아가고, 기능도 좋은 설문조사 서비스를 던져주면 어떨까? 모든 불만사항을 해결해주면 어떤 반응이 나올까?

# 1. 선택

내가 직접 처음부터 끝까지 설문조사 서비스를 구축하면 그 또한 좋겠지만, 아쉽게도 본업이 너무 바빴다. 그래서 self hosting이 가능한 적당한 솔루션을 찾기 시작했다. 2개가 물망에 올랐다. 결론은 [LimeSurvey]다.

1. [TellForm] : *Free Opensource Alternative to TypeForm or Google Forms* 라는 당당한 자기소개가 맘에 들었지만, 아쉽게도 [Docker] 지원이 뻑났다. 이래서야 환경설정하기가 너무 귀찮아진다. 현상을 해당 [repository](https://github.com/tellform/docker_files)에 로그와 함께 issue로 등록해주었다.
1. [LimeSurvey] : 기능 충실하고, 문서 충실하고, 사용하기도 편하고, [Docker] 덕에 환경설정도 편하다. comunity edtion은 공짜다. 이걸로 선택.

# 2. 설치

아주 간단하다. 다음과 같이 하면 된다. 서버의 OS는 [Ubuntu 16.04.3 LTS](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes?_ga=2.245309125.1154947023.1510894532-877806816.1510894532)이다.

## 1. docker pull

[여기](https://hub.docker.com/r/crramirez/limesurvey/)의 도움을 받았다.

```bash
docker pull crramirez/limesurvey
```

## 2. docker-compose.yml 작성

나는 80포트는 달리 돌리고 있는 서비스가 있어서 LimeSurvey에는 8080포트를 부여하기로 했다.

```bash
version: '2'
services:
  limesurvey:
    ports:
      - "8080:8080"
    volumes:
      - mysql:/var/lib/mysql
      - upload:/app/upload
    image:
      crramirez/limesurvey:latest
volumes:
  mysql:
  upload:
```

## 3. 실행

위에서 만든 docker-compose.yml을 저장한 경로에서 도커 컨테이너를 실행한다.

```bash
docker-compose up -d
```

이제 브라우저에서 http://localhost:8080 로 접속해보면 LimeSurvey 화면이 뜰 것이다.

## 4. 간단한 설정

LimeSurvey 설치 화면이 나오는데, DockerHub의 Usage에 나온대로 해주면 된다. 아래에도 옮겨봤다.

* Database type MySQL
* Database location localhost
* Database user root*
* Database password
* Database name limesurvey #Or whatever you like
* Table prefix lime_ #Or whatever you like

# 3. 끝

이제 다 되었다. 그냥 쓰면 된다. 신난다!

[Docker]: <https://www.docker.com>
[LimeSurvey]: https://www.limesurvey.org/
[TellForm]: https://www.tellform.com
[구글 설문조사]: https://www.google.com/forms/about/