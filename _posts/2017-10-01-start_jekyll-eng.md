---
layout: single
title: Building a Blog with Jekyll
date: 2017-10-01 23:07:30.000000000 +09:00
type: post
header:
  teaser: "https://jekyllrb.com/img/logo-2x.png"
  image: "https://jekyllrb.com/img/logo-2x.png"
categories:
- IT
tags: [jekyll]
---

## 0. Getting Started
I decided to take up blogging again. It was an attempt to shake off, even a little, my long-standing laziness and inertia.
The essence of blogging should of course be writing, but being the kind of person I am, I couldn't help fixating on the tools right from the start.
Specifically, the task of finding and setting up a suitable blogging tool—and from this very stage I ended up pouring in far more effort than expected, which dragged things out.
Still, there were a few small revelations to be had as a developer, so I console myself that it wasn't entirely a waste of time.
From here, I want to briefly write down the process of deliberation I went through. I hope it turns out to be of some small, unexpected help to someone.

## 1. Choosing a Tool
Until now, I've drifted through a number of blogging tools. [Blogger], [egloos], [WordPress.com], even [tumblr]... The last one I used, [WordPress.com], had a problem I didn't like: conflicts between plugins. Fortunately, there were plenty of options. Each had its own pros and cons, so I couldn't rank them definitively. In the end, I decided based on my own taste and needs.
- [WordPress] : Unless absolutely necessary, I didn't want to learn php from scratch.
- [Medium] & [brunch]: Their strengths are beautiful design and excellent reach. But the limited freedom they offer was a dealbreaker.
- [JHipster] : I thought it might be fun to build a personal homepage as if doing an SI project, and JHipster was very appealing because it lets you quickly set up a Spring Boot + Angular4 development environment. But since I couldn't guarantee when on earth I'd actually be able to start blogging, I saved it for another day.
- [jekyll] : Its heyday has passed. But its big advantage is being developer-friendly. Above all, building it as a github page would fill in my github contributions graph, which had been rather barren lately, and that was nice.

## 2. Setup and Migration
The initial setup was very simple. After following the steps in the [jekyll Quick-start guide], you just create a github repository and git push, and that's it. Windows users apparently have to do something rather annoying, but my desktop runs Mint Linux and my laptop is a MacBook, so I sailed right through. It's the default theme, but it looks clean and pleasant. Migrating the old posts from my previous blog on [WordPress.com] was also surprisingly easy to finish. As they say, when you follow the technologies that everyone else uses, you'll at least come out around the middle of the pack.

## 3. Choosing a Theme
It would have been nice if I'd stopped at step 2 and focused on blogging in earnest, but being the pitiful soul who obsesses over trivial details, I started looking for any way I could to make the blog a little prettier. Fortunately, there were themes built by predecessors who took pity on people like me. Once you apply a theme, it takes care of essential plugins like comments without you having to worry much about them. You can get themes from marketplaces ([jekyllThemes.io], [jekyllrc theme], [jekyllTheme.org]). There are lots of pretty and practical themes. There are even fun ones like the [windows95 theme]. I went with [minimal mistake]. It had many users, lots of stars and forks on github, and was diligently updated. Once again, this was development by following the crowd.

## 4. Applying the Theme
This is where I went through a lot of trial and error. The challenges and solutions were as follows.
1. Theme configuration: The [minimal mistake] theme has a fair number of features, so there was quite a bit to configure. I had to set up the _config.yml file line by line while referring to the theme's Quick-start documentation and examples.
1. Front Matter: To apply jekyll, you have to write something called front matter at the top of every document, whether it's html or markdown. It's the part between three dashes (---) that holds the post's meta information, and if you don't get it right, jekyll can't build. This is the only new thing you need to learn for blogging with jekyll.
1. ruby, gem: I don't know ruby. I know gem even less. But jekyll is made with ruby, so I had to face unfamiliar syntax accordingly. Fortunately, you can solve most problems by following the official documentation, and when you're still stuck, the god of Google will tell you, so you get used to it soon enough. As for the rest, the theme you're applying takes care of it on its own.
1. Korean: Apparently, with some themes, Korean characters get garbled. [minimal mistake] is one of them. After a long struggle, I decided to take the easy route and just write in English. After all, when talking to computers, using anything other than English, numbers, and bits only makes life harder.

## 5. Choosing a Markdown Editor
Now that the blog is in place, I had to pick an editor. Writing posts in html would be fine, but I decided not to bother. I'm not a publisher anyway, and I have no sense for writing pretty html. There were a few options here too. I didn't consider paid editors. The conclusion was Visual Studio Code.
1. WebStorm: Since it's the tool I always use for front-end development, I fired this up first without a second thought. You just install a plugin that supports markdown preview. But thinking that there's no way I'd need an entire IDE just to write posts, I rejected it.
1. Visual Studio Code: The strongest text editor around right now. Naturally, there's a plugin that supports Markdown. You can use it with confidence.
1. [stackEdit] : An editor with the scent of a high-class developer. It runs in the web browser. Even Visual Studio Code can't match stackEdit. Its only downside, if you can call it one, is that it's web-based.

## 6. Search Engine Exposure
Either way, since it's a blog, I need to do at least a minimal amount of self-promotion. To do that, you have to get exposed on search engines, but apparently github pages aren't exposed to search engines until you configure it separately. It's a hassle, but it's something you have to go through once. Google and Bing are supported by most themes. You just verify your site, go into each one's webmaster tools, and register something like a sitemap.xml. Since this is a blog with Korean as the main language, I also have to register with Naver and Daum. This too can be done just by following the guides. For Naver, I added support to [minimal mistake] and sent a pull request. I'm not sure whether they'll accept it, though.

## 7. Wrapping Up
The first time I ever took an interest in personal homepages was in the year 2000 AD, when the afterglow of the millennium hadn't yet faded. As a middle school student back then, I couldn't even dream of getting my hands directly on things like html or servers, and had to make do with sneaking glances at the Namo web editor, which was already quite primitive even by the standards of the time. More than a dozen years later, now that I've become a mid-level developer (a foreman of grunt work), times have changed: the personal homepage has been reduced to an ancient relic, and it's become the age of social media and blogs (and even those are quite past their prime). Now, having built a blog of my own at last—however humble—I've finally laid to rest a long-held regret, even if only in part. But the process was utterly different from what I had imagined as a child. I built a blog that builds with ruby even though I don't know ruby, and it was at a level that even non-developers could manage without much difficulty. I'm reminded of something a senior developer I once worked with on a project said after coming back from a Unity conference. He said the era of developing by being good at a single language is over. The role of a developer will probably keep changing. There's nothing to do but diligently keep up.

[egloos]: <http://egloos.com>
[Medium]: <https://medium.com>
[brunch]: <https://brunch.co.kr>
[WordPress]: <https://wordpress.org>
[jekyll]: <https://jekyllrb.com>
[JHipster]: <http://www.jhipster.tech>
[WordPress.com]: <https://wordpress.com>
[Blogger]: <https://www.blogger.com>
[tumblr]: <https://www.tumblr.com>
[jekyll Quick-start guide]: <https://jekyllrb.com/docs/quickstart>
[minimal mistake]: <https://github.com/mmistakes/minimal-mistakes>
[jekyllThemes.io]: <https://jekyllthemes.io>
[jekyllrc theme]: <http://themes.jekyllrc.org>
[jekyllTheme.org]: <http://jekyllthemes.org>
[windows95 theme]: <http://jekyllthemes.org/themes/windows-95>
[stackedit]: <https://stackedit.io>
