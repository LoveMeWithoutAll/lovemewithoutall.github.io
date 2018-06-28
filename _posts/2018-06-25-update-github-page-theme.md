---
layout: single
title: github page theme 업데이트
date: 2018-06-25 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://jekyllrb.com/img/logo-2x.png"
    image: "https://jekyllrb.com/img/logo-2x.png"
categories:
- IT
tags: [jekyll]
---

이 블로그는 [jekyll]을 사용하여 돌리고 있다. 테마는 [minimal mistake]를 사용한다. 요 테마는 업데이트가 상당히 활발한데, 멋진 블로그를 위해 숟가락 얹은 처지인지라 블로깅 하는 틈틈히 테마 업데이트를 따라가줘야 한다. 테마 업데이트 방법은 다음과 같다. 나는 github page에서 블로그를 호스팅한지라, git을 사용하여 업데이트 하는 방법을 썼다.

## 1. upstream branch 등록

[minimal mistake] 저장소의 마스터 브랜치를 remote로 등록한 후,

```bash
git remote add upstream https://github.com/mmistakes/minimal-mistakes.git
```

## 2. pull

pull 받는다.

```bash
git pull upstream master
```

## 3. merge conflict를 해결한다

## 4. commit 후 push한다

끝!

## 참조

https://mmistakes.github.io/minimal-mistakes/docs/upgrading/

[jekyll]: <https://jekyllrb.com>
[minimal mistake]: <https://github.com/mmistakes/minimal-mistakes>
