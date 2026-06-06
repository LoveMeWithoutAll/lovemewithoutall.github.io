---
layout: single
title: When AWS CloudFront Can't Find S3's index.html
date: 2021-10-18 23:23:30.000000000 +09:00
type: post
header:
    teaser: "https://www.pngfind.com/pngs/m/56-565278_amazon-cloudfront-logo-png-amazon-cloudfront-logo-transparent.png"
    image: "https://www.pngfind.com/pngs/m/56-565278_amazon-cloudfront-logo-png-amazon-cloudfront-logo-transparent.png"
categories:
- IT
tags: [aws]
---

## The Symptom
I requested the index.html file via CloudFront -> S3, but got this error:
![confusing](/assets/images/2021-10-18-cloud-front-s3-index.png)

## The Cause
When CloudFront -> S3 forwarded the request, it passed the HTTP request's url (dev-live11-view.11st.co.kr) as the host header. But the S3 bucket address can't be found with that host (because it doesn't exist).

## The Solution
When forwarding the request from CloudFront -> S3, strip out the host header before passing it along.
Of course, the request-forwarding configuration from CloudFront -> S3 needs to be set up properly beforehand.

20210902
