---
layout: single
title: Keeping AWS CodeDeploy build artifacts
date: 2021-11-18 20:35:30.000000000 +09:00
type: post
header:
    teaser: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
    image: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
categories:
- IT
tags: [aws]
---

You need to set it to '**/*' so that files in subdirectories are recursively included in the artifact. It is mentioned in the AWS docs too... but it's explained rather vaguely, so it's easy to miss.

If you use the default settings of create-react-app, the build output ends up under the build directory. In that case, you can configure it as follows.

```
artifacts:
  files:
    - ./build/**/*
  discard-paths: no
```

20211022
