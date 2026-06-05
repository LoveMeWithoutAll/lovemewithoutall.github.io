---
layout: single
title: R.I.P SK Planet
date: 2018-04-05 00:07:30.000000000 +09:00
type: post
header:
    teaser: "https://www.skplanet.com/images/kor/common/footer/h2_sk_planet.png"
    image: "https://www.skplanet.com/images/kor/common/footer/h2_sk_planet.png"
categories:
- Diary
tags: [SK Planet, diary, dev-diary, JavaScript]
---

These days I'm developing an iOS app with Swift. It's not a native app but one that loads an already-built web page in a web view, and as I was busily working away, the screen suddenly went white and fell silent. This was a feature that had been working just fine only a few seconds earlier.

When I debugged it, T-Map turned out to be the problem. The screen in question used T-Map's API to display a campus map and provide walking-navigation guidance to a desired location. This T-Map API had been cut off. The cause was that the SK Planet Developer Center, which had previously provided the T-Map API service, was being shut down, and in the middle of handing the T-Map service over to its parent company SK Telecom, the API server was temporarily—or perhaps permanently—cut off. Watching SK Planet, once my dream company, fade into the dustbin of history left me feeling quite bitter. It hit me all the more since, as an enthusiastic fan (?) of location-based services, I had even gone to a conference hosted by SK Planet.

In any case, I had to take some action right away. Discarding the API key provided by the now-defunct SK Planet and getting a new one issued by SK Telecom to display the campus map again went fine. The problem was the walking-navigation feature, which spewed errors from T-Map's JavaScript file and flatly refused to work. Only the company has changed, the service is supposed to be identical, right? Code that had clearly worked just fine when using the API service provided by SK Planet did not work with SK Telecom's API. After confirming that the API hadn't changed, that it wasn't conflicting with another JavaScript library, and that there was no problem with the HTTP requests being sent to T-Map, I concluded that this was simply a bug in T-Map.

But whatever the case—whatever problem T-Map had—I needed to get the walking-navigation service up and running immediately. How? Of course, no method came to mind right away. Half resigned to my fate, I was reading through the T-Map API documentation when I noticed that the sample code for the walking-navigation API differed from the API I had been using. The old API hadn't disappeared, of course, but this sample code using the new API was working just fine. I quickly reached this conclusion: rather than wasting time reporting the error to SK Telecom and waiting for a reply, I'd just build the walking-navigation service anew using the API that this sample code uses!

Fortunately, the API documentation and sample code were excellent, so I was able to develop it quickly. But there was a separate, nagging issue. As I quietly examined the existing code, it really didn't suit my taste. To what degree? To the point where, without realizing it, the words "why on earth did they build it like this?" slipped right out of my mouth. That screen, written with jQuery, had its code structured around DOM manipulation and event handling, and that very approach was not to my liking. Functions were calling other functions far too much, and far too often.

Frustrated, I scrutinized the code even more carefully, but it wasn't particularly messy and there was no real cruft. Still, I couldn't help feeling unsatisfied. I even wondered whether it was because I'd been developing with Vue.js lately, but at the time I wrote that code I was already using AngularJS fairly well, so it didn't seem to be due to whether or not I was using a front-end framework. In the end, it comes down to my coding taste having changed, and this was the first time I'd realized that I myself had changed so drastically in just ten months. It struck me all the more because I hadn't had many experiences of confronting code I'd written after time had passed. What on earth is happening to me? Has the rapidly changing world of JavaScript driven me to this? Is this a good thing or not? I honestly have no idea. The future, even less so.

In any case, the SK Planet Developer Center left me with no small amount to think about right up to its final moments. Rest in peace.

20180403
