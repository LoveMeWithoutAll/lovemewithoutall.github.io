---
layout: single
title: 리눅스에 Java10 설치하기
date: 2018-04-15 17:40:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn.pixabay.com/photo/2017/05/19/21/12/java-2327538_960_720.png"
    image: "https://cdn.pixabay.com/photo/2017/05/19/21/12/java-2327538_960_720.png"
categories:
- IT
tags: [Java]
---

Linux에서 Java10을 설치하는 방법을 정리해보았다. 우분투와 리눅스 민트에서 다 잘 설치된다. [linuxuprising](https://www.linuxuprising.com/2018/04/install-oracle-java-10-in-ubuntu-or.html)의 문서를 참조하였다.

## 1. 설치 준비

왜 그런지는 모르겠지만, 2018년 4월 15일 현재, apt-get에 Java10이 올라가 있지 않다. 이 글을 읽고 있는 지금은 혹시 올라왔을지 모르니 확인해보자.

```bash
sudo apt-cache search java10
```

apt-get에 아직 없다면 PPA repository에서 설치하자.

```bash
sudo add-apt-repository ppa:linuxuprising/java
sudo apt update
```

## 2. 설치

이제 Oracle Java10을 설치하자.

```bash
sudo apt install oracle-java10-installer
```

파란 화면에 당황하지 말고 라이선스에 동의해야 설치가 진행된다. 설치에는 시간이 꽤 걸린다.

우분투에서는 Oracle Java10이 자동으로 default로 설정되지만, 리눅스 민트 등의 다른 배포판에서는 따로 설정을 해줘야한다.

```bash
sudo apt install oracle-java10-set-default
```

이걸로 설치가 끝났다.니

## 3. 확인

설치가 잘 되었는지 확인해보자

```bash
java -version
```

Oracle Java10이 정상적으로 default로 설치됐다면 다음과 같은 메시지가 뜰 것이다.

```bash
java version "10" 2018-03-20
Java(TM) SE Runtime Environment 18.3 (build 10+46)
Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10+46, mixed mode)
```

javac도 확인해보자.


```bash
javac -version
```

설치가 잘 됐다면 이런 메시지가 뜬다.

```bash
javac 10
```

이제 Java10을 써보자!

