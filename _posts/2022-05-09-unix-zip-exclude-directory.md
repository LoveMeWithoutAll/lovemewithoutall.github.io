---
layout: single
title: unix zip exclude directory
date: 2022-05-09 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/395/934/png-transparent-computer-icons-zip-file-zip-free-icon-miscellaneous-angle-rectangle.png"
    image: "https://w7.pngwing.com/pngs/395/934/png-transparent-computer-icons-zip-file-zip-free-icon-miscellaneous-angle-rectangle.png"
categories:
- IT
tags: [linux]
---

소스 코드를 압축할 때 `.git` 디렉토리나 `node_modules`을 포함할 필요는 없다. 제외하자.

```bash
zip -r zip_file_name.zip . # 현재 경로의 모든 파일을 하위 디렉토리 포함하여 압축한다
zip -r zip_file_name.zip . -x '.git*' # .git 디렉토리 아래의 모든 파일은 압축 대상에서 제외한다
zip -r zip_file_name.zip . -x 'node_modules*' # node_modules 디렉토리 아래의 모든 파일은 압축 대상에서 제외한다
```

20220502
