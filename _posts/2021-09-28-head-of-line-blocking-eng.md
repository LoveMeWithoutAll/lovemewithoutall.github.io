---
layout: single
title: HOL (Head of line blocking)
date: 2021-09-28 02:16:30.000000000 +09:00
type: post
header:
    teaser: "https://miro.medium.com/max/2233/0*DvW52URHvIUtt9i7"
    image: "https://miro.medium.com/max/2233/0*DvW52URHvIUtt9i7"
categories:
- IT
tags: [network]
---

HOL (head of line): latency caused by transmission delays in TCP

1. Cause: TCP enforces a strict transmission order. If a TCP packet that needs to be transmitted first is lost, all other packets are blocked until that packet is retransmitted.

2. In HTTP/1.1: A limitation arises in data transmission with HTTP pipelining (this is not about queuing requests on the client, but about queuing responses on the server. The client sends multiple requests at once, and the server processes the requests it receives). If the response to an earlier request cannot be fully sent, later transmissions are delayed as well. The later transmissions pile up in the server's buffer and consume resources. Because of this, it can be exploited to attack the server. And if a response fails, all subsequent requests have to be sent again. => HTTP pipelining became a failed technology.

3. In HTTP/2.0: It is gone. This is thanks to request and response multiplexing becoming possible. Since multiple requests and responses can be exchanged in parallel over a single TCP connection, nothing gets blocked.

20210928
