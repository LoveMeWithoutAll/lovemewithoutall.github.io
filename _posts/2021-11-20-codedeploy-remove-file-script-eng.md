---
layout: single
title: A bash script to delete files in AWS CodeDeploy whose LastModified is more than 30 days old
date: 2021-11-20 21:54:30.000000000 +09:00
type: post
header:
    teaser: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
    image: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
categories:
- IT
tags: [aws]
---

When old deployment files pile up in AWS S3, they become hard to manage. The more frequently you deploy, the worse it gets. So let's delete files that are 30 days or older based on their modification date.

There were countless scripts on StackOverflow to solve this problem, but they only worked fine locally (MacOS zsh), and on CodeBuild every single one of them threw an error like this:

```bash
Error parsing parameter '--delete': Expected: '=', received: ']' for input:
Objects=[{Key=},],Quiet=true
                ^
```

This error probably happens because the OS and shell used by CodeDeploy's Docker container are different from my local environment (my guess).

So I just wrote my own script.
This one runs on CodeDeploy. For real.

```bash
#!/bin/bash

SINCE=`date --date '-4 weeks -2 days' +%F 2>/dev/null || date -v '-4w' -v '-2d' +%F` # day +30 days
bucket=bucket_name # s3 bucket

aws s3api list-objects-v2 --bucket $bucket --query 'Contents[?LastModified < `'"$SINCE"'`]' --output  text > file-of-keys # query S3 for files older than the cutoff based on their last modified date (LastModified) and generate a file list

cat file-of-keys | awk '{print $2}' | xargs -P8 -n1000 -L 1 bash -c 'aws s3 rm s3://'$bucket'/$0' # read the file | pick out only the 2nd column (file name) | run the bash script one line at a time | the command to run is s3 rm =^ㅅ^=
```

20211025
