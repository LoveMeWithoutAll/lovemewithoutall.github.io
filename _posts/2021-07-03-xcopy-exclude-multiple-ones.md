---
layout: single
title: xcopy에서 여러 개의 파일 및 디렉토리를 제외하고 복사하기
date: 2018-07-03 11:45:30.000000000 +09:00
type: post
header:
    teaser: "https://www.computerhope.com/cdn/dos/xcopy-command.gif"
    image: "https://www.computerhope.com/cdn/dos/xcopy-command.gif"
categories:
- IT
tags: [IT]
---

tl:dr: [xcopy]의 **/exclude:** 옵션에 제외할 목록을 기술한 **txt** 파일을 넣으면 여러 파일과 디렉토리를 제외하고 복사할 수 있다.

다음은 [xcopy]를 사용하여 복사를 할 때, 여러 개의 디렉토리 및 파일을 제외하고 복사하는 방법이다.

exclude 옵션을 넣을 때, 파일명이나 디렉토리 명이 아닌 txt 파일을 넣는데,

```cmd
rem xcopy command
xcopy src경로 destination경로 /exclude:.\exclude-list.txt
```

txt 파일에는 디렉토리 혹은 파일을 기술한다.

```bash
# .\copy-exclude-list.txt
\.git\ # 디렉토리 제외
.css # CSS 파일 제외
test* # test로 시작되는 파일명 제외
# 등등
```

끝!

[xcopy]: https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/xcopy