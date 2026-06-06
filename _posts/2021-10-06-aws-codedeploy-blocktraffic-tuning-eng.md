---
layout: single
title: Reducing AWS CodeDeploy BlockTraffic Time
date: 2021-10-06 23:58:30.000000000 +09:00
type: post
header:
    teaser: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
    image: "https://d1.awsstatic.com/product-marketing/CodeDeploy/Partners/ALMsuite_AWSCodePipeline.47e93c2726522cd49c8c948bbfc608a63cadd256.png"
categories:
- IT
tags: [AWS]
---

I was running Apache HTTP Server on AWS EC2 to serve a frontend, and using AWS CodePipeline for CI/CD. The problem was that every time I deployed, the BlockTraffic stage of CodeDeploy took way too long, which was frustrating. For anyone looking to shave even a little time off their deployments, I'm sharing a tuning point. I myself heard about it from a cloud developer at my company.

In

`ec2 > load balancing > Target groups > ... > attribute > edit`

just lower the Deregistration delay.

After changing it from 300 to 60, the BlockTraffic time was reduced by 80%.

Awesome!

20210729
