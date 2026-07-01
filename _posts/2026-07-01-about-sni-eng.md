---
layout: single
title: SNI 
date: 2026-07-01 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://raw.githubusercontent.com/wingsuitist/ecmascript-logo/master/es-ecmascript-logo.png"
    image: "https://raw.githubusercontent.com/wingsuitist/ecmascript-logo/master/es-ecmascript-logo.png"
categories:
- IT
tags: [network]
---

Until recently, I thought I had a rough understanding of SNI (Server Name Indication). However, while working with AWS, I realized that my understanding had been incomplete.

I used to think of SNI as:

> "Information used to associate an HTTP request with the correct domain on a server hosting multiple domains."

This isn't entirely wrong, but it's incomplete. More importantly, I never really understood **when** or **how** SNI was actually used in the technologies I work with. I vaguely assumed it was something like the HTTP `Host` header.

It turns out that SNI is **not** used during the HTTP request phase. Instead, it is used during the **TLS handshake**.

For example, when AWS CloudFront initiates an HTTPS connection to an Application Load Balancer (ALB), it must first establish a TLS handshake. During this process, SNI acts as a signpost that tells the ALB, *"Use the TLS certificate for this domain."* The ALB reads the SNI value and selects the appropriate TLS certificate for the requested domain. Only after the TLS handshake has completed does the HTTP communication begin.

From the perspective of the OSI model, SNI belongs to the Session layer (Layer 5) because it is exchanged during the TLS handshake. Since TLS is also responsible for encryption, SNI is closely related to the Presentation layer (Layer 6) as well.

Now I understood **when** SNI is used. The next question was: **What is SNI actually derived from?**

The answer is surprisingly simple. **The hostname in the URL becomes the SNI.**

In fact, the URL's hostname is reused throughout the entire connection process. It is used for the DNS lookup to resolve the server's IP address, included in the SNI during the TLS handshake so the server can select the correct certificate, and finally placed in the HTTP `Host` header once the encrypted connection has been established.

The same hostname is reused multiple times for different purposes throughout the lifecycle of a single HTTPS request.


EOD

20260701
