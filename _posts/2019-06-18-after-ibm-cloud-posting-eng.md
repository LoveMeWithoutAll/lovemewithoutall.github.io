---
layout: single
title: A Follow-up After Writing the IBM Cloud Promo Post
date: 2019-06-18 18:07:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
- IT
tags: [IBM Cloud]
---

A few days ago, after publishing my [promo post](https://lovemewithoutall.github.io/it/ibm-cloud/) about [IBM Cloud], I found a few things I needed to update, so I'm writing a separate post.

## 1. Outage Notifications

On 2019.06.17, I received an email from IBM with the following content. It said there was a login failure on the customer portal. The symptom of this outage is the same as [the outage I experienced](https://lovemewithoutall.github.io/it/ibm-cloud/), but the date and time are different.

```
Start Date: Monday 17-Jun-2019 00:00 UTC
Event Type: Unplanned Incident
Subject: Event 83348051 - customer portal and api to be unavailable 
Severity: Severity 1

=================================================================
/ Latest Update /
- As of Monday 17-Jun-2019 15:55 UTC
As of 2019-06-17 14:54, services have returned to normal.

/ Items Associated With This Event /
Item ID | Hostname | Public IP | Private IP | Item Type


/ Update History /
- As of Monday 17-Jun-2019 15:55 UTC
As of 2019-06-17 14:54, services have returned to normal.

- As of Monday 17-Jun-2019 14:54 UTC
Engineers have returned our services to normal operation. 

We thank you for your patience while we worked on this outage.

- As of Monday 17-Jun-2019 13:48 UTC
ON 06/17 AT 11:57 AM (UTC), An issue was identified that caused our customer portal and api to be unavailable to customers. We have engineering teams investigating this outage and are currently working to restore these services.

Thank you for your patience in this matter.

=================================================================This email was generated automatically. Do not reply to this email.

```

But this is the first time I've ever received an email like this. Maybe the system for sending out outage notification emails was built just within the last few days? Then again, building an email notification system like this isn't all that hard. I'd guess that the reason I never got these notification emails back when I experienced outages in the past was that my account was at a low tier (I hadn't even registered a payment card).

## 2. On the Frequent Changes

In my previous post, I complained that the service changes far too quickly. Apparently this was a complaint shared by many. Perhaps that's why today (2019.06.18) I received this email.

```
(...beginning omitted...)
Some links in the document have been updated to reflect new URLs that are part of our recent change from bluemix.net to cloud.ibm.com addresses. We have also reformatted all links in the document as text, not just hypertext links, in response to a common customer request
(...end omitted...)
```

It's a notice that the URLs of the official documentation are changing. That's fine by me, but I do wish they'd make the links in the old docs redirect to the new docs.

## Anyway, I recommend IBM Cloud!
