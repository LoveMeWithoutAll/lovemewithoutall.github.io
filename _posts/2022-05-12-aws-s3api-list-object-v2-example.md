---
layout: single
title: AWS s3api list-object-v2 사용 예시
date: 2022-05-12 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Amazon_Web_Services_Logo.svg/2880px-Amazon_Web_Services_Logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Amazon_Web_Services_Logo.svg/2880px-Amazon_Web_Services_Logo.svg.png"
categories:
- IT
tags: [AWS]
---

S3의 lastModified는 사용자가 변경할 수 없다. 이것은 S3의 사용 제한 사항이다(기능이다).

console에서 테스트하는 쿼리

```bash
➜  aws s3api list-objects-v2 --bucket 11st-dev-live11-admin-static --query 'Contents[?LastModified > `2022-04-29 17:45:53`]' --output text | grep map | awk '{print $2, $3}'
```


s3는 버킷의 aws region 따라 lastModified가 정해진다. 한국 리전이면 한국 시간(UTC+9)이다
codeBuild는 region과 무관하게UTC +0 기준이다.
s3api는 실행 환경과 무관하게 UTC+0 기준이다.
따라서 한국 리전의 버켓에 있는 오브젝트를 LastModified를 기준으로 조회하려면, 한국 시간을 기준으로 조회해야 한다. 그러니 9시간을 더해줘야 한다.

s3api를 사용해 생성된지 하루가 지나지 않은 파일만 골라 copy하는 예시

```bash
SINCE=`date --date '-1 days' +%F 2>/dev/null || date -v '-1d' +%F` # day -1일
aws s3api list-objects-v2 --bucket 11st-dev-live11-admin-static --query 'Contents[?LastModified < `'"$SINCE"'`]' --output text | grep map | awk '{print $2}'
    while read -r line; do \
    aws s3api copy-object \
       --bucket  11st-dev-live11-admin-static\
       --cache-control "max-age=600" \
       --content-type "application/json" \
       --copy-source "11st-dev-live11-admin-static/$line" \
       --metadata-directive "REPLACE" \
       --key "$line"; \
    done
```

s3api copy-object의 --copy-source-if-modified-since 사용 예제
--copy-source-if-modified-since 의 파라미터는 unix timestamp다. 따라서 date를 +%s 로 변환해야 한다

```bash
aws s3api copy-object \
--bucket bucket-name \
--copy-source "file-name.extension" \
--key "target-file-name.extenstion" \
--metadata-directive "REPLACE" \
--copy-source-if-modified-since `date --date '-1 days' +%s 2>/dev/null || date -v '-1d' +%s` # LastModified가 현 시점으로부터 1일 미만 지난 파일만 복사ㅎ나다
```

20220429
