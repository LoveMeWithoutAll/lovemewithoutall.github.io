---
layout: single
title: AWS CodeDeploy BlockTraffic 시간 단축
date: 2021-10-06 23:58:30.000000000 +09:00
type: post
header:
    teaser: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
    image: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
categories:
- IT
tags: [AWS]
---

AWS EC2에 Apache HTTP Server를 올려 frontend를 서빙하고, CI/CD는 AWS CodePipeLine을 사용하고 있었다. 문제는 배포할 때마다 CodeDeploy의 BlockTraffic 단계에서 너무 많은 시간이 걸려 답답했다. 배포 시간을 조금이나마 줄이고자 하는 분들을 위한 튜닝 포인트를 공유한다. 나도 같은 회사 클라우드 개발자 분에게 들은거다.

`ec2 > load balancing > Target groups > ... > attribute > edit`

에서 Deregistration delay를 낮추면 된다.

300 -> 60으로 수정하니 BlockTraffic 시간이 80% 단축되었다.

신난다!

20211006
