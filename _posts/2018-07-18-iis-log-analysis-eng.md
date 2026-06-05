---
layout: single
title: IIS Log Analysis
date: 2018-07-16 17:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/iisstart.png"
    image: "/assets/images/iisstart.png"
categories:
- IT
tags: [IIS, log]
---

# Cottage-Industry-Style Log Analysis

When you operate a service, there are many times you need to analyze logs. Many of the services I operate use [IIS](https://msdn.microsoft.com/ko-kr/library/hh831725%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396), and for whatever reason, no real-time log analysis system for the web server had been set up. It would be nice to set up something like [GoAccess](https://goaccess.io/), but I have neither the authority nor the resources to make that decision. I suppose the thinking is that things run fine without it... Anyway, every now and then the need to analyze logs comes up. So I did some log analysis cottage-industry style.

# Environment

## Server
* Windows Server 2008 R2
* IIS 7.5
* Log type: IISW3CLOG

## My Machine

* Windows 10

# Log Parser

1. Install [Log Parser 2.2](https://www.microsoft.com/en-us/download/confirmation.aspx?id=24659).

1. Run the script.

1. The script below counts HTTP requests whose *uri* contains *aspx*, grouped by URI. The syntax is almost identical to SQL.

```cmd
logparser -i:W3C -o:csv "SELECT cs-uri-stem, COUNT(*) AS Hits into d:\results.csv FROM d:\u_ex180612.log WHERE cs-uri-stem like '%.aspx%' GROUP BY cs-uri-stem ORDER BY Hits DESC"
```

# Log Parser Studio

There is an application that helps you run Log Parser conveniently. It's called [Log Parser Studio](https://gallery.technet.microsoft.com/Log-Parser-Studio-cd458765), and although it has been around for quite a while, it lacks nothing for cottage-industry purposes. After downloading and unzipping it, the included *README.txt* file explains how to use it well. Then again, it's built so cleanly that you hardly need any usage instructions at all.

The end!
