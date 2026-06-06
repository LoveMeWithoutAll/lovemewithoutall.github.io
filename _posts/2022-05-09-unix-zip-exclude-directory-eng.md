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

When you compress source code, there's no need to include the `.git` directory or `node_modules`. Let's exclude them.

```bash
zip -r zip_file_name.zip . # compress all files in the current path, including subdirectories
zip -r zip_file_name.zip . -x '.git*' # exclude all files under the .git directory from compression
zip -r zip_file_name.zip . -x 'node_modules*' # exclude all files under the node_modules directory from compression
```

20220502
