---
layout: single
title: A Retrospective on 11Kitties FE Development
date: 2024-05-07 09:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend]
---

About a month has passed since the launch on April 1. The service has stabilized, and additional development is steadily underway. Now is a good time for a retrospective. I want to go through the retrospective stage by stage, following the project's progression.

## Analysis

It was my first time developing in the game domain, but the planning team's requirements seemed simple. The cat eats food, accumulates experience points, and when it levels up you draw a new cat. The technical challenges identified during the analysis stage were roughly "frequent fix deployments are needed" and "make the cat move."

Looking back, my consideration of the technical challenges was insufficient. This was due to my lack of experience in the game domain.

## Design

Design is a rough sketch for solving the technical challenges. So I worked out a method for each of the technical challenges identified during the analysis stage.

* Frequent deployments -> develop with a WebView
* Moving images -> use a local cache

I already had plenty of experience solving these kinds of problems, so I wasn't worried in the slightest. The only concern was the tight development schedule. Of course, the lack of development time was serious. But even this wasn't a big worry. Pulling off seemingly impossible development schedules is what I've been doing all along.

For the technologies used, refer to [The technologies used in developing 11Kitties and a review of using them](https://lovemewithoutall.github.io/it/11kitties-tech/).

## Development

As long as the design is done well, development is merely a matter of time. But I've never experienced a project like that. This time too, there were several challenges discovered during development. Let me look at each one.

### Premature (and wrong) image caching optimization

To make a cat move, each cat required downloading image files of at least 60Mb. I asked the design team to reduce the image file sizes, but they refused. Their reasoning was that lowering image quality leads to lowering the game's quality.

I tried to solve this problem the textbook way. The strategy was to use the browser's local storage to cache the cat's state, to store images in the browser cache for a long time based on the cat's state, and then to preload the images. But 60Mb is an enormous size, and with the issue of CDN costs as well, there was a need for further improvement. So I belatedly added a PWA layer to directly control the browser cache. iOS's in-app browser didn't support the PWA cache API, which was somewhat troublesome, but I soon found a workaround. The download speed was satisfyingly fast. However...

A problem occurred on older mobile devices. The cat images were over 10Mb each, and on older devices, even applying a local cache to the image files still left rendering taking a long time. In the end, I got the design team's approval and solved the problem by greatly reducing the image file sizes to 2~3Mb. Looking back, this was the more textbook solution than caching. The caching and PWA-related code still worked, but I deleted all of it to reduce the application's complexity. With an already tight schedule, I essentially wasted time needlessly adding complex caching logic and configuration.

### Complex state branching and interdependencies

If the image caching optimization ended as a mere incident, the complex state-branching handling nearly caused a disaster. Even for the same user input, the game's behavior had to differ greatly depending on the state. Here, the state had to include various cases such as the cat's state, level, and special events, and it had to be possible to modify the logic via deployment at any time during operation. Game development was different from typical web application development.

![calculator infinite state machine](https://miro.medium.com/v2/resize:fit:4800/format:webp/1*QhktO3TSniCjsuMC_vaL3w.jpeg)
<i>Obviously, a game is more complex than a calculator (image source: https://rvunabandi.medium.com/making-a-calculator-in-javascript-64193ea6a492)</i>

I managed to implement the behavior with just React's useState and zustand, but the complexity was considerable and maintenance didn't look easy. So after much deliberation, and urgently, I decided to adopt xstate. As with the Live11 project before, there was the option of implementing a finite state machine directly, but with the tight development schedule I used an already-proven library. There were difficulties: xstate's features were overkill, more than this project needed, the learning curve was considerable, and since it had only recently been upgraded to v5, references were scarce. But in the end, adopting xstate was a success. The declaratively described state machine robustly expressed the level-up process, this project's most complex process. The code became so clean that no bugs were even found during the QA period. Even after the project launched, thanks to xstate, adding, modifying, and deleting logic were all handled conveniently and simply. Choosing the right solution determines a project's success or failure.

What I regret is that I didn't use xstate everywhere a complex process needed to be implemented. That's because, back when I first adopted xstate, I wasn't confident about its usefulness. As a result, the processes where I didn't adopt xstate had to suffer through long debugging and bugs.

## BE Integration

The BE integration, which would have taken two days in other projects, was such a struggle that it took four days in this project. The complexity of having 60 APIs was likely the primary cause, but I suspect it was also because there wasn't enough discussion between the BE and FE sides starting from the analysis and design stages. There were many items that we only discussed at the BE integration stage—what the planning requirements were, and what each API field meant. This is something to improve on in future projects.

## Testing

Perhaps because of the struggle during the BE integration stage, not many bugs were found during the QA period. Contrary to upper management's concerns, no extension of the development period was needed either. I'm satisfied with this point.

## Overall Assessment

This was a high-intensity project where two developers made over 2000 commits in a month. I'm grateful to my teammate Kim Byeong-ho, who kept up well.

Although there were flaws in the analysis and design stages due to my lack of game development experience, I was able to solve the challenges by boldly adopting the right solutions. This was a fortunate outcome in the end, but on the other hand it leaves some regret. If there had been enough preliminary research on the difficulties of game development and on libraries like xstate, I think the elements of uncertainty during the project could have been greatly reduced.

20240507
