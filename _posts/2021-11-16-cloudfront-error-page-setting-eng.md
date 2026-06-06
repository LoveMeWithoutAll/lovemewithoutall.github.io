---
layout: single
title: Things to Watch Out for When Configuring an AWS CloudFront Error Page
date: 2021-11-16 21:00:30.000000000 +09:00
type: post
header:
    teaser: "https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png"
    image: "https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png"
categories:
- IT
tags: [aws]
---

In CloudFront (hereafter CF), error page redirection can only happen once per request. It does not run more than once. Let's look at an example.

1. A 400 error occurs, so it redirects to the '/404' path
2. There is no resource at the '/404' path, so it redirects to '/index.html/404'

The two redirections above will not work.

Why does CF block this kind of behavior? Even the AWS contact on the project I work on didn't know the exact reason. My guess is that they blocked it to prevent circular references between redirections.

In frontend frameworks like React and Vue.js, you almost always use case 2 from the example, so you need to be careful when configuring CF error page rules.

20211014
