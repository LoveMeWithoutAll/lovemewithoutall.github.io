---
layout: single
title: What happens when you send an HTTP request to CloudFront with a nonexistent method (HTTP Verb)?
date: 2021-11-02 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png"
    image: "https://seeklogo.com/images/A/aws-cloudfront-logo-35A2EA80AB-seeklogo.com.png"
categories:
- IT
tags: [aws]
---

What happens when you send an HTTP request to CloudFront (hereafter CF) with a nonexistent method (HTTP Verb)?

Generally, when a request comes into CF
1. A TCP connection is established
2. It passes through TLS
3. It reaches the CloudFront distribution

But if the method doesn't exist, it doesn't get as far as step 3. Since it never reaches step 3, the error page redirection rule doesn't kick in. Instead, CF responds with the 400 error HTML that is set by default in CF (which cannot be customized by the user).

20211015
