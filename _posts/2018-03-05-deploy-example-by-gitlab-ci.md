---
layout: single
title: Gitlab-CI 구성 & .gitlab-ci.yml 예제
date: 2018-03-05 19:07:30.000000000 +09:00
type: post
header:
  teaser: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
  image: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
categories:
- IT
tags: [Git, Gitlab, Gitlab-CI, CI, CD]
---

## CI/CD 도입
 [Gitlab을 설치](https://lovemewithoutall.github.io/it/start-docker/)하는 방법은 전에 다뤘다. 이번에는 CI/CD 도구를 도입했다. 예전에 일했던 프로젝트에서는 주로 [jenkins]를 사용했는데, 이번에는 [Gitlab-CI]를 써보기로 했다. Git 초심자가 대부분인 우리 프로젝트 특성상, [Gitlab]과 연계가 긴밀한 [Gitlab-CI]를 선택하는 편이 Git 보급에 유리할 듯 싶었다. [Gitlab-CI]과 [jenkins]의 비교는 하기 링크를 참조했다. 물론 애시당초 [jenkins]와 [Gitlab-CI]는 같은 선상에서 비교할만한 도구도 아니긴 하다.

### [Gitlab-CI] versus [jenkins]
 * [내가 만든 Gitlab-CI가 최고](https://about.gitlab.com/comparison/gitlab-vs-jenkins.html)
 * [jenkins가 좋지만 Gitlab의 가능성을 본다](https://www.inovex.de/blog/modern-cicd-with-jenkins-2-and-gitlab-ci-comparison/)

## [Gitlab-CI] 구성
[올라운드플레이어님의 이 글](http://allroundplaying.tistory.com/21)에 설명이 잘 되어 있다. 그냥 따라하기만 하면 된다. 너무 좋아서 뽀뽀해주고 싶을 정도. [Gitlab-CI 공식 문서]도 충실하다. 하지만 **gitlab-runner**가 어떤 작업을 수행할지 설정해줘야 한다. 이건 .gitlab-ci.yml 에 기입해줘야 한다.

## .gitlab-ci.yml 구성
이제 내가 원하는 작업을 **gitlab-runner**가 수행하도록 **.gitlab-ci.yml**를 짜보자. 이 또한 그리 어렵지 않다. **script:** 아래의 한 줄은 **gitlab-runner register**에서 7번째로 설정하는 **Please enter the executor**에 입력한 executor에서의 명령어 한 번씩이다. 예를 들어 **shell**을 executor로 선택하고 하기 스크립트로 **.gitlab-ci.yml**을 구성하면,

```bash
script:
    - echo hello
    - dir
```

아래와 같은 결과가 나온다.

```yml
hello # echo hello의 결과
c:\blah\blah\blah # dir의 결과
```

## 예제: 단순 배포
이제 소스코드를 copy & paste 하는 단순 작업 수행 **.gitlab-ci.yml**을 구성해보자. **only** 는 git의 어느 branch의 변화에 반응하느냐를 기술한 것이다. 

예를 들어

```yml
    only:
    - master
```
는 master 브랜치에 커밋이나 변경 사항이 있을 때만 동작한다.

하기 **.gitlab-ci.yml** 예시는 각각 master 브랜치와 develop 브랜치에 커밋이나 변경 사항이 있으면, 각각 하기의 script를 실행한다. Windows 환경에서 shell을 executor로 하여 xcopy 명령어를 활용하였다.

```
deploy-to-production-server:
    only:
    - master
    script:
    - xcopy . D:\운영서버의\코드들어갈_위치\ /h /k /y /e /r /d

deploy-to-develop-server:
    only:
    - develop
    script:
    - xcopy . D:\개발서버의\코드들어갈_위치\ /h /k /y /e /r /d
```

허접하지만 단순 배포를 위해서는 잘 돌아간다. Anyway, It works!

[Gitlab-CI]: https://about.gitlab.com/features/gitlab-ci-cd/
[Gitlab-CI 공식 문서]: https://docs.gitlab.com/ee/ci/README.html
[jenkins]: https://jenkins.io/
[Gitlab]: <https://gitlab.com>