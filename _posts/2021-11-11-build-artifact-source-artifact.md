---
layout: single
title: AWS codeBuild에서 build artifact와 source artifact
date: 2021-11-10 19:14:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-11-11-build-artifact-source-artifact.png"
    image: "/assets/images/2021-11-11-build-artifact-source-artifact.png"
categories:
- IT
tags: [aws]
---

build artifact는 codeBuild가 build를 마친 후, 그 결과물을 저장한 파일. 나중에 auto scaling 할 때 쓰인다
source artifact는 codeBuild가 build를 하기 위해 소스코드를 가져온 파일

codeBuild를 돌리면 둘 다 만들어진다

업로드 되는 위치는 CodeBuild > Build projects > 빌드 선택 > 하위 메뉴에서 > Build details > Artifacts 에서 설정할 수 있다.

![artifacts](/assets/images/2021-11-11-build-artifact-source-artifact.png)

20211018
