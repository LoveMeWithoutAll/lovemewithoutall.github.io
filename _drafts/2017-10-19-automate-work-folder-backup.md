---
layout: single
title: git으로 작업 문서 폴더 백업 자동화하기
date: 2017-10-19 18:42:30.000000000 +09:00
header:
  teaser: "/assets/images/Git-Logo-2Color.png"
  image: "/assets/images/Git-Logo-2Color.png"
type: post
categories:
- IT
tags: [git, automate, shell]
---

나는 게으른 사람이다. 그리고 뭔가를 백업하는건 아주 귀찮은 일이다. 소스코드는 그나마 낫다. 개발하고 수정할 때마다 git remote 서버로 push하면 된다. 하지만 작업용 및 관리용으로 들고 있는 work 폴더를 백업하는건 너무나도 귀찮은 일이다. 그러니 자동화하기로 했다. 작업 단계는 이렇다.

1. work 폴더의 변경사항을 git으로 commit 하는 쉘스크립트를 만든다.
2. 쉘스크립트를 매일 1회 배치로 돌아가게 한다.

아래는 내가 돌리고 있는 쉘스크립트다. 

```bash
#! /bin/bash
cd /d/work
git status
git add .
git commit -m $(date +"%Y%m%d_%T")
git push -u origin master 
```

이걸로 내 인생을 매일 1분씩 아낄 수 있게 되었다.