---
layout: single
title: docker로 gitlab 설치하기
date: 2017-10-02 23:07:30.000000000 +09:00
header:
  teaser: "https://www.docker.com/sites/default/files/Whale%20Logo332%402x_5.png"
type: post
categories:
- IT
tags: [docker, gitlab]
---

## 1. git으로 간다
회사에 git을 도입하기로 했다. 그간 내가 다니는 회사는 svn을 사내 버전관리 도구로 사용하고 있었는데, 나는 머리가 좋지 않아 git이 없으면 개발을 하질 못한다. 언제 뭘 만들고 고치고 지웠는지 도무지 기억할 수가 있어야지... 이점에서 **git의 장점은 명확하다. history를 알 수 있다.** 다행히 git을 쓰자는 나의 건의가 받아들여졌고, 이를 위한 제반 작업도 내가 전담하기로 했다.

## 2. [gitlab]으로 간다
소스 코드는 반드시 회사 내부 서버에 보관해야 한다는 회사 방침에 의거, 따로 git 서버를 구축해야 했다. 놀고 있는 데스크톱을 하나 받아 리눅스(Ubuntu 16.04 LTS)부터 깔았다. 리누스 토발즈... 저는 언제쯤 당신의 손아귀에서 벗어날지... git 서버로 어느걸 사용할지 잠시 고민했다. 결론은 [gitlab]이다.
- [yona] : 네이버에서 많이 쓴다고 한다. 비개발자도 업무에 유용하게 사용할 수 있어 좋다고 한다. 다만 jenkins 등 CI 도구와의 연계가 잘 되는지 확인할 수 없었기에 기각.
- [gitlab] : 과거 여러 프로젝트에서 썼기에 익숙하고, CI 도구와의 연계도 이미 검증됐다. 레퍼런스도 많다. 부화뇌동... 뇌동개발.

## 3. [docker]로 간다
여태껏 OS에 직접 설치하는 방법으로 gitlab을 사용해왔다. 하지만 이번에는 [docker]를 사용해보기로 했다. docker를 써보고 싶다는 것 외에 달리 이유는 없었다. 개발자로 밥벌이하려면 이슈가 되는 기술은 한번쯤 건드려보고 지나가야한다고 생각한다. 물론 docker가 이슈가 된지는 꽤 됐지만... 아무튼 곧 보겠지만 후회는 없었다.

## 4. docker & gitlab 설치
### docker
docker 설치는 간단했다. [우분투 도커 설치 가이드] 에 나온대로 따라하면 끝이다. apt-get은 좋은 것이다.
### gitlab
명령어 한번이면 된다. 실행까지 해준다. [gitlab 커뮤니티 에디션] 기준이다. `--hostname` 옵션의 url은 적당히 바꿔준다. `--publish` 옵션의 22는 22번 포트를 쓴다는거니 ssh가 올라와 있다면 적당히 설정해준다. ':' 을 기준으로 앞의 숫자가 실물 서버(이하 host)의 포트고, 뒤의 숫자가 host와 연결된 docker의 포트다. docker는 하나의 서버처럼 인식되어 host와 통신하기에 이런 설정을 해줘야 한다. `--volume` 옵션은 다음 절에서 설명한다. `--restart` 옵션을 줬기 때문에 OS를 재기동해도 docker가 gitlab을 알아서 띄워준다. 더이상의 자세한 설명은 [가이드](https://docs.gitlab.com/omnibus/docker/README.html)를 참고하면 된다.
```
sudo docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```
잘 실행되는지 웹브라우저를 열고 http://127.0.0.1 에 접속해본다. 네트워크 상의 다른 컴퓨터에서는 브라우저에서 host의 ip로 접속할 수 있다. gitlab 로그인 페이지가 뜨면 잘 된 것이다. 

## 5. docker-compose
이대로 써도 좋지만 저 많은 옵션들을 기억해야 하는건 아름답지 않는다. 나 같은 사람을 위해 docker-compose가 있다. 나중에 CI 도구와 연계하기 위해서도 필요하다. 우선 이미 만들어진 컨테이너를 지워야한다. 여기서 간단히 짚고 넘어가야 할 것이 이미지와 컨테이너다. 이미지는 컨테이너 설치를 위한 cd로 생각하면 된다. 컨테이너는 host에 설치된 서버다. 데이터를 들고 있다. 그러므로 컨테이너를 지우면 데이터는 다 날아간다. 그럼 컨테이너 지우면 안되는거 아니냐고? 이건 다음 절에서 설명한다.
```
sudo docker stop gitlab
sudo docker rm gitlab
```
docker-compose의 설치는 [가이드](https://docs.gitlab.com/omnibus/docker/README.html#install-gitlab-using-docker-compose)가 있으니 그대로 따라하면 된다. 그 다음 가이드에서 시키는대로 `docker-compose.yml` 파일을 만들고 명령어를 실행하면 된다. 마찬가지로 url과 port는 상황에 맞게 적당히 적는다.
```
docker-compose up -d 
```
이제 gitlab 컨테이너를 다시 만들어 띄웠다. 지금 당장 쓸 필요는 없지만 앞으로 gitlab 컨테이너를 띄울 때는 이 명령어로 한다.
```
sudo docker start gitlab
```

## 6. backup & update
만약의 사태를 대비하여 gitlab 서버의 데이터를 백업해두라는 지시를 받았다. 여기서 긴 삽질을 해야했다. 컨테이너를 어떻게 백업하지? 데이터는 컨테이너에 다 저장된다며? 컨테이너를 이미지로 만들어서 복사해둬야 하나? 처음에는 그렇게 생각했다. 그렇게 해도 된다. 하지만 그럴 필요 없다. 4절의 docker run 옵션 중 `--volume` 을 기억할 것이다. 이게 뭐냐면 컨테이너의 해당 폴더는 host의 특정 폴더로 사용하겠다는 옵션이다. 그리고 gitlab을 사용하면서 쌓이는 데이터는 git 커밋 데이터를 포함하여 모두 `--volume` 옵션에서 지정한 세 폴더 안에 쌓인다. 그러므로 저 세 폴더만 백업하면 된다. 복구할 때는 백업해둔 세 폴더를 `--volume` 옵션에서 설정해둔 세 폴더로 옮겨둔 후, gitlab 컨테이너를 run 하면 된다. gitlab의 버전을 업데이터 할 때도 마찬가지다. gitlab 컨테이너를 지우고, 도커 이미지를 업데이트 한 후, 그 이미지로 컨테이너를 만들면 된다. 백업 방법은 여러가지로 구성할 수 있겠지만, 나는 [samba]로 NAS를 마운트 한 후, crontab으로 매일 1회 압축파일을 떨구는 걸로 했다. 죄는 랜섬웨어에 있지 [samba]에 있지 않다.

## 7. 마무리
처음 만난 docker는 편리하고 강력했다. 이 좋은걸 왜 이제서야 써보나 싶을 정도로. 나는 개발하다가 기분이 좋아지면 키보드를 꽝꽝 내리찍으며 타이핑을 하는 버릇이 있는데, docker를 쓰고선 3일 동안 키보드를 내리찍었다. 오히려 이렇게 좋을거라고 도저히 믿기 어려워 다방면으로 테스트하느라 많은 시간을 낭비해야 했다. 이 글의 독자분들은 나처럼 괜한 의심으로 인생을 낭비하지 않길 바란다.

[yona]: <https://repo.yona.io/>
[gitlab]: <https://gitlab.com>
[docker]: <https://www.docker.com>
[우분투 도커 설치 가이드]: <https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-using-the-repository>
[gitlab 커뮤니티 에디션]: <https://hub.docker.com/r/gitlab/gitlab-ce/>
[samba]: <https://www.samba.org>