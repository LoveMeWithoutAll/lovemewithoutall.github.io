---
layout: single
title: An Unpaid Promotional Post for IBM Cloud
date: 2019-06-17 18:07:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
- IT
tags: [IBM Cloud]
---

[IBM Cloud] is a really great service, yet I've never seen anyone use it. At most, maybe some developers who work at IBM. Feeling that it's a shame, I'm writing this promotional post without getting paid. The advantages of IBM Cloud are as follows.

## Advantages

1. Free
    - Unlike other clouds, it doesn't lure you in with sign-up credits. There's simply a free usage plan called the lite plan.
    - Even though it's free, it's by no means slow. Quite usable.
1. Convenient
    - It uses Cloud Foundry's CLI.
    - Thanks to that, the CLI tool is very easy and powerful to use.
    - It's far easier to use than the CLI tools from Company A, Company M, or Company N.
    - Just type the cf push command and your app gets deployed (!).
    - If you want to learn more about how convenient it is, check out the posts I've written.
        - [Deploying a Telegram bot built with Node.js on IBM Cloud](https://lovemewithoutall.github.io/it/deploy-on-ibm-cloud/)
        - [Deploy python telegram bot on IBM BlueMix](https://lovemewithoutall.github.io/it/deploy-python-bot-on-IBM-bluemix/)
        - [Deploy Node.js telegram bot on IBM BlueMix](https://lovemewithoutall.github.io/it/deploy-bot-on-IBM-bluemix/)
1. It satisfies the developer hipster vibe. Because nobody else uses it.

But it's not all upsides. Maybe because it's a minor cloud service, every now and then something massive goes wrong, and I've never heard anything about how they handle it. It seems they probably don't handle it properly.

## The incidents I experienced

1. The account disappeared
    - I had an account running an [app (a Telegram chatbot)](https://github.com/LoveMeWithoutAll/HanGifBot) that I built as a toy project, but at some point I could no longer log in.
    - The login error message said the account was invalid? blocked?
    - I called and emailed IBM asking them to restore my account, but it got escalated to IBM's U.S. headquarters and after that there was no response.
    - So I abandoned that account. Even now, two years later, that account is still inaccessible.
1. Login outage
    - This happened over a few hours in the afternoon on 2019.06.14, Korean time.
    - I couldn't log in to [IBM Cloud]. It wasn't even a login failure. It was just a response delay that never ended.
    - I wasn't the only one. A few other people experienced it at the same time as me.
    - If something like this had happened with Company A's cloud, the world would have been in an uproar. It's an unfortunate situation for IBM.

## Other minor inconveniences I experienced

* Like all cloud services these days, it's evolving at an incredibly fast pace. `It even changes the CLI tool's commands abruptly.` Then again, they even changed the name (the old name was Bluemix), so what's the big deal about changing CLI commands?

## Wrapping up

I'll say it again: [IBM Cloud] is a truly convenient, free, and great service. Do you really need to run a simple toy project on a paid cloud service? I strongly recommend `IBM Cloud`.

[IBM Cloud]: https://www.ibm.com/kr-ko/cloud
