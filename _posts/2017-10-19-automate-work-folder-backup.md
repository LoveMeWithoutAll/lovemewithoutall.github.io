---
layout: single
title: git으로 work 폴더 백업 자동화하기
date: 2018-06-29 18:42:30.000000000 +09:00
header:
  teaser: "/assets/images/Git-Logo-2Color.png"
  image: "/assets/images/Git-Logo-2Color.png"
type: post
categories:
- IT
tags: [git, automate, shell, backup]
---

나는 게으른 사람이다. 그리고 뭔가를 백업하는건 아주 귀찮은 일이다. 소스코드는 그나마 낫다. 개발하고 수정할 때마다 git remote 서버로 push하면 된다. 하지만 작업용 및 관리용으로 들고 있는 work 폴더를 백업하는건 너무나도 귀찮은 일이다. 그러니 자동화하기로 했다. 작업 단계는 이렇다.

1. work 폴더의 변경사항을 git으로 commit 하는 스크립트를 만든다.
1. 스크립트를 매일 1회 배치로 돌아가게 한다.

Windows7과 Windows10에서의 설정법을 각각 정리해보았다.

## 1. Windows7

아래 쉘 스크립트를 만들어

```bash
#! /bin/bash
cd /d/work # 내가 백업할 폴더
git status
git add .
git commit -m $(date +"%Y%m%d_%T")
git push -u origin master
```

작업 스케줄러에 등록한다.

## 2. Windows10

Windows Subsystem for Linux(WSL) 덕분에 SSH Key 관리가 다소 복잡해졌다. [이 블로그](http://webdir.tistory.com/547)의 글을 참조하여 SSH Key를 만든 후, 아래 batch 스크립트를 작업 스케줄러에 등록한다(no shell script! bat)

```cmd
@echo off
for /f "tokens=2 delims==" %%a in ('wmic OS Get localdatetime /value') do set "dt=%%a"
set "YY=%dt:~2,2%" & set "YYYY=%dt:~0,4%" & set "MM=%dt:~4,2%" & set "DD=%dt:~6,2%"
set "HH=%dt:~8,2%" & set "Min=%dt:~10,2%" & set "Sec=%dt:~12,2%"
set "datestamp=%YYYY%%MM%%DD%" & set "timestamp=%HH%%Min%%Sec%"
set "fullstamp=%YYYY%-%MM%-%DD%_%HH%-%Min%-%Sec%"

REM d:\work 가 백업할 폴더
d:
cd work
git add .
git commit -m "%fullstamp%"
git push -u origin master 
```

이걸로 내 인생을 매일 1분씩 아낄 수 있게 되었다.

> **Tip:** **MS Office** 문서 파일을 실행하면 **~$**로 시작하는 임시 파일이 생성되는데, 이 파일 때문에 commit이 안되는 현상이 발생한다. 그러니 gitignore에 등록해주자. 아래처럼 만들어주면 된다.

```bash
~$*
```

끝!

참고: https://stackoverflow.com/questions/11037831/filename-timestamp-in-windows-cmd-batch-script/11037921
