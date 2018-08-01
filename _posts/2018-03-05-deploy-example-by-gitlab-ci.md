---
layout: single
title: Gitlab-CI 구성 & .gitlab-ci.yml 예제
date: 2018-07-04 14:07:30.000000000 +09:00
type: post
header:
  teaser: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
  image: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
categories:
- IT
tags: [Git, Gitlab, Gitlab-CI, gitlab-runner, CI, CD]
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

배포 target 서버에 **gitlab-runner**를 *register*한 후, 하기 **.gitlab-ci.yml** 예시를 돌려보자. 
master 브랜치와 develop 브랜치에 push가 들어오면, 각각 하기의 script를 실행한다. 
Windows 환경에서 shell을 executor로 하여 xcopy 명령어를 활용하였다.

```yml
# master 브랜치에 push가 들어올 때 동작하는 job
deploy-to-production-server:
    only:
    - master
    script:
    - xcopy . D:\운영서버의\코드들어갈_위치\ /h /k /y /e /r /d

# develop 브랜치에 push가 들어올 때 동작하는 job
deploy-to-develop-server:
    only:
    - develop
    script:
    - xcopy . D:\개발서버의\코드들어갈_위치\ /h /k /y /e /r /d
```

허접하지만 단순 배포를 위해서는 잘 돌아간다. Anyway, It works!
하지만 개발서버와 운영서버를 따로 구축해놓은 경우에는 조금 복잡해진다. 
또한 운영 중인 서비스가 많을 경우, 서비스마다 **gitlab-runner**를 *register*하기도 어렵다.

## 예제: Shared Runners를 활용한 원격 배포

위 단순 배포 예제의 문제점을 해결하려면 **Shared Runners**로 원격 서버의 디스크를 마운트하여 배포하면 된다. 
단순 배포 예제에서처럼 하나의 프로젝트에 하나의 **gitlab-runner**를 등록하는 경우를 **Specific Runners**라고 하는데, 
**Shared Runners**와 **Specific Runners**의 차이점은 [공식 문서](https://docs.gitlab.com/ce/ci/runners/README.html#shared-vs-specific-runners)에 잘 나와있다. 
특별히 복잡한 스크립트를 실행하여 특별 관리가 필요한 프로젝트가 아니라면 **Shared Runners**를 사용하는게 편하다. 
**Shared Runners**의 설정 방법은 [여기](https://docs.gitlab.com/ce/ci/runners/README.html#registering-a-shared-runner)를 참고하면 된다. 
아래 **.gitlab-ci.yml**은 Windows 환경에서 shell을 executor로 돌린다. 

```yml
deploy to production:
    stage: deploy # 따로 설정해두지 않으면 기본값인 test로 박힌다
    environment:  # 서버별로 따로 설정해두는 편이 나중에 롤백하기 편하다
      name: production server
    only:
    - master
    script:
    - echo 'deploy to production server'
    - 'net use o: \\서버IP\소스코드_경로 서버접속PW /user:서버접속ID' # 배포할 원격 서버의 경로를 o드라이브로 잡는다
    - 'xcopy . o:\ /h /k /y /e /r /d' # 배포
    after_script: # script실행시 에러가 발생해도 이 after_script 구문은 반드시 실행한다
    - 'net use /delete o:' # 원격 드라이브의 연결을 해제

deploy to develop:
    stage: deploy
    only:
    - develop 
    - branches # 가끔 급히 branch를 push해서 개발서버에 배포해야 할 때도 있다
    except:
    - master # master 브랜치와는 확실히 분리해준다. 예시를 위해 추가한 구문이니 빼도 상관없다
    environment:
      name: develop server
    script:
    - echo 'deploy to develop server'
    - 'net use o: \\서버IP\소스코드_경로 서버접속PW /user:서버접속ID'
    - 'xcopy . o:\ /h /k /y /e /r /d'
    after_script:
    - 'net use /delete o:'

```

## 예제: stages와 cache 적용

위 예제에서는 1번의 배포를 할 때 1개의 **job**을 실행했다.
이번 예제에서는 **stage**와 **cache**를 추가했다.
1. **stages**를 등록하면 여러 개의 **job**을 순차적으로 실행할 수 있다. 
1. **cache**를 설정하면 과거에 수행한 **job**에서 만든 캐시를 사용할 수 있다. 따라서 **npm install**처럼 인터넷에서 라이브러리 등을 매번 다운받아야 하는 지점에 사용하면 배포 시간을 단축할 수 있다. 자세한 내용은 [공식 문서](https://docs.gitlab.com/ee/ci/caching/index.html#) 참조.

```yml
# deploy -> foreign_survey_build 순서로 job이 실행된다
# deploy 먼저 실행하는 이유는, npm install과 build에 시간이 많이 걸리기 때문이다
stages:
  - deploy
  - foreign_survey_build

# 모든 job이 끝날 때마다 after_script를 실행한다
after_script:
  - 'net use /delete o:'

# 모든 job의 이름은 unique 해야 한다
deploy-to-dev:
  stage: deploy # deploy 차례(stage)에 실행되는 job이다
  environment:
    name: development server
  only:
    - develop
    - branches
  except:
    - master
  script:
    - echo 'deploy to develop server'
    - 'net use o: \\서버IP\소스코드_경로 서버접속PW /user:서버접속ID'
    - 'xcopy . o:\ /h /k /y /e /r /d /exclude:.\gitlab-ci-copy-exclude-list.txt'

deploy-to-prod:
  stage: deploy # stage: deploy인 job이 2개가 있는데, 이럴 경우에 두 job은 병렬로 실행된다
  environment:
    name: production server
  only:
  - master    
  script:
    - echo 'deploy to production server'
    - 'net use o: \\서버IP\소스코드_경로 서버접속PW /user:서버접속ID'
    - 'xcopy . o:\ /h /k /y /e /r /d /exclude:.\gitlab-ci-copy-exclude-list.txt'

foreign-survey-build-to-dev:
  stage: foreign_survey_build
  only:
    - develop
    - branches
  except:
    - master
  environment:
    name: development server
  script:
    - echo 'foreign_survey is being builded to develop server'    
    - cd foreign_survey\client # 일부 모듈만 npm을 사용하여 이렇게 구성했다
    - call npm install # cache가 있고 package.json이 변경되지 않았으면, cache로 저장된 파일(압축 저장되어 있음)을 사용한다.
    - call npm run build
    - 'net use o: \\서버IP\소스코드_경로 서버접속PW /user:서버접속ID'
    - 'xcopy dist\. o:\foreign_survey\client\dist\ /h /k /y /e /r'
  cache:
    key: ${CI_COMMIT_REF_SLUG} # 이 브랜치(master)에서 만들어둔 cache를 사용한다
    paths:
    - foreign_survey/client/node_modules/ # cache할 디렉토리를 정의한다

foreign-survey-build-to-prod:
  stage: foreign_survey_build
  environment:
    name: production server
  only:
  - master    
  script:
    - echo 'foreign_survey is being builded to production server'
    - cd foreign_survey\client
    - call npm install
    - call npm run build
    - 'net use o: \\서버IP\소스코드_경로 서버접속PW /user:서버접속ID'
    - 'xcopy dist\. o:\foreign_survey\client\dist\ /h /k /y /e /r'
  cache: # cache는 job을 실행하면서 존재 여부를 check하고, script를 다 실행한 이후에 다시 한 번 cache한다.
    key: ${CI_COMMIT_REF_SLUG}
    paths:
    - foreign_survey/client/node_modules/
```

위 예제에서는 branch를 기준으로 cache를 저장한다. 하지만 하나의 상위 프로젝트 안에 여러 개의 하위 Node 프로젝트가 들어 있고, 각각의 stage에서 build를 하는 경우에는 branch만을 기준으로는 cache를 저장해봐야 소용없다. 아래처럼 설정하면 stage & branch 별로 cache를 저장할 수 있다.

```yml
# ...
cache:
    key: "$CI_JOB_STAGE-$CI_COMMIT_REF_SLUG"
# ...
```

동일 branch 내에서 cache를 공유하는 방법에 대한 자세한 내용은 [여기](https://docs.gitlab.com/ee/ci/caching/#sharing-caches-across-the-same-branch)를 참조하시라.

배포 지옥에서 해방되길 간절히 바랍니다!

끝!

[Gitlab-CI]: https://about.gitlab.com/features/gitlab-ci-cd/
[Gitlab-CI 공식 문서]: https://docs.gitlab.com/ee/ci/README.html
[jenkins]: https://jenkins.io/
[Gitlab]: <https://gitlab.com>