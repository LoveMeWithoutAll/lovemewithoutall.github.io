---
layout: single
title: AWS s3api list-object-v2 Usage Example
date: 2022-05-12 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Amazon_Web_Services_Logo.svg/2880px-Amazon_Web_Services_Logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/Amazon_Web_Services_Logo.svg/2880px-Amazon_Web_Services_Logo.svg.png"
categories:
- IT
tags: [AWS]
---

S3's lastModified cannot be changed by the user. This is a usage restriction of S3 (it's a feature).

A query to test in the console

```bash
➜  aws s3api list-objects-v2 --bucket 11st-dev-live11-admin-static --query 'Contents[?LastModified > `2022-04-29 17:45:53`]' --output text | grep map | awk '{print $2, $3}'
```


For S3, lastModified is determined by the bucket's AWS region. If it's the Korea region, it uses Korean time (UTC+9).
codeBuild uses UTC+0 regardless of the region.
s3api uses UTC+0 regardless of the execution environment.
Therefore, to query objects in a Korea-region bucket based on LastModified, you have to query based on Korean time. So you need to add 9 hours.

An example using s3api that selects and copies only files created less than a day ago

```bash
SINCE=`date --date '-1 days' +%F 2>/dev/null || date -v '-1d' +%F` # one day earlier
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

An example using the --copy-source-if-modified-since option of s3api copy-object
The parameter for --copy-source-if-modified-since is a Unix timestamp. Therefore, you have to convert date with +%s.

```bash
aws s3api copy-object \
--bucket bucket-name \
--copy-source "file-name.extension" \
--key "target-file-name.extenstion" \
--metadata-directive "REPLACE" \
--copy-source-if-modified-since `date --date '-1 days' +%s 2>/dev/null || date -v '-1d' +%s` # Copies only files whose LastModified is within the last day from now
```

20220429
