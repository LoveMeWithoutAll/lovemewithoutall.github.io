---
layout: single
title: AWS CodeDeploy에서 LastModified가 30일 지난 파일들을 삭제하는 bash script
date: 2021-11-20 21:54:30.000000000 +09:00
type: post
header:
    teaser: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
    image: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
categories:
- IT
tags: [aws]
---

AWS S3에 예전 배포 파일들이 쌓여있으면 관리하기 어렵다. 배포를 자주 할 수록 더욱 그렇다. 그러니 파일 변경일을 기준으로 30일 이상 지난 파일을 삭제해보자.

StackOverflow에 이 문제를 해결하기 위한 수많은 스크립트가 올라와 있었으나, 로컬(MacOS zsh)에서만 잘 동작할 뿐, CodeBuild에서는 하나같이 이런 오류를 발생시켰다.

```bash
Error parsing parameter '--delete': Expected: '=', received: ']' for input:
Objects=[{Key=},],Quiet=true
                ^
```

아마도 CodeDeploy의 도커 컨테이너가 사용하는 OS와 shell이 내 로컬 환경과 달라서 발생하는 오류인듯 하다(추정).

그래서 그냥 내가 스크립트를 만들었다.
이건 CodeDeploy에서 돌아간다. 진짜임.

```bash
#!/bin/bash

SINCE=`date --date '-4 weeks -2 days' +%F 2>/dev/null || date -v '-4w' -v '-2d' +%F` # day +30일
bucket=bucket_name # s3 bucket

aws s3api list-objects-v2 --bucket $bucket --query 'Contents[?LastModified < `'"$SINCE"'`]' --output  text > file-of-keys # s3에서 최종수정일(LastModified)이 기준으로 오래된 파일 조회하여 파일 목록 생성

cat file-of-keys | awk '{print $2}' | xargs -P8 -n1000 -L 1 bash -c 'aws s3 rm s3://'$bucket'/$0' # 파일을 읽어와서 | 2번째 컬럼(파일명)만 추려내어 | 1line씩 bash 스크립트 실행 | 실행할 명령은 s3 rm =^ㅅ^=
```

20211025
