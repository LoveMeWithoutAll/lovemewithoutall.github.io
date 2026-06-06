---
layout: single
title: The Tech Stack I Used to Build 11 Kitties and My Thoughts on It
date: 2024-04-02 09:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
    image: "/assets/images/2024-04-02-11kitties-tech/game-image.png"
categories:
- IT
tags: [frontend]
---

After 40 days of development, I launched 11 Kitties.

![game](/assets/images/2024-04-02-11kitties-tech/game-image.png)

Let me go through the technologies I used to build this game about raising cute cats, one by one, along with the ones I tried to use but ended up dropping.

## Technologies I Used

### Development Environment

1. Bun
    1. Why I chose it: it's fast
    2. Thoughts
        * Pros
            1. It's really fast.
                1. Build speed is more than 3x faster than `node`
                1. The local server starts instantly
            3. The documentation is pretty friendly
        * Cons
            1. There are few references for troubleshooting
            4. It has a lot of bugs.
                1. Using bun on its own is fine. But problems arise when you use it together with other tools.
                2. Example: with the default settings alone, I couldn't do a `production build`. There was a bug where bun's environment variable settings conflicted with `Vite`'s environment variable settings, so it always ended up doing only a `development build`. To work around this, I had to add a script that copies the environment-specific `.env` file to the project root directory every time I build.
            1. Lack of references for the test suite. As a result, I couldn't use `github copilot`'s automatic test code generation feature as-is; I had to tweak the generated test code a bit. But it works well.
            5. To work with `pwa`, you need the dependency overrides shown below
            1. The fact that it includes a test suite is one of bun's selling points. But `vitest` is better. Node's test feature is fast enough, and above all, AI doesn't know `bun test`'s code.
            1. Can bun replace node?
```json
"overrides": {
  "@rollup/plugin-node-resolve": "^15.2.3"
}
```
        
2. biome
    1. Why I chose it
        1. `eslint/prettier` doesn't work properly in `webstorm`
        2. I can't stand the configuration conflicts between `eslint` and `prettier`
    2. Thoughts
        1. It works well
        2. References are extremely scarce
        3. It integrates reasonably well with WebStorm (there are bugs, though)
        4. I'm worried that biome might not properly support react 18. There's already a lot of chatter about this in the biome community too. That's the downside of a small open-source project.
3. Vite
    1. Why I chose it: it's the de facto standard
    2. Thoughts: fast and easy to configure.

### Libraries

1. React 18
    1. Why I chose it
        1. Familiarity
        2. A rich ecosystem
        3. Power
        4. Career value
    2. Thoughts: satisfied
2. Zustand
    1. Why I chose it
        1. It beat out the other stores and became the trend
        2. Convenient `localStorage` support
    2. Thoughts
        1. More convenient syntax compared to `mobx`, which I used before
        2. It's nice that you can choose the store scale appropriately
        3. The learning curve is gentle
        4. Kind of similar to `Vue pinia`?
        5. I used localStorage recklessly for the sake of optimization and got a bitter taste of premature optimization.
3. xState
    1. Why I chose it
        1. During game development, I needed a finite state machine to handle sequential && multiple && conditional events depending on state branching. Without xstate, my react components would have fallen into conditional-statement hell.
        2. Others use it, so I figured I'd give it a try too...
    2. Thoughts
        * Pros
            1. The functionality is good
            1. Thanks to clean declarative code, it's flexible for feature changes and easy to maintain
            1. The above advantage is overwhelming, so I plan to keep using it despite the various drawbacks described below
        * Cons
            1. The documentation is shoddy
            3. The types are shoddy too. With a well-made library, you can guess how to use it just by looking at the types, but xstate isn't like that. It's generic hell.
            4. There are few references for v5, the latest version and the one I used
            5. Debugging the functionality itself is hard
            6. The learning curve is steep
            7. Overkill compared to a finite state machine I'd implement myself
            9. To debug, you have to rely heavily on `actors.getSnapshot().value`
1. React-query(v4)
    1. Why I chose it
        1. I needed query caching
        2. It's familiar and useful
        4. I wanted to use v5, the latest version, but it doesn't support ios12...
    2. Thoughts
        1. I'm always unhappy with the documentation
        2. Still, it's better than V5, where references are scarce
        3. As for types, v4 is still generic hell

## Technologies I Tried but Dropped

1. Pwa = workbox + vitePWA
    1. Why I chose it: runtime caching of the large images the game needs
    2. Thoughts
        1. The configuration is incredibly convenient
        2. I was frustrated that `apple` doesn't support `pwa`'s `caches api`
        3. It was tricky to handle cases where the Response type was `opaque`. I had a hard time because I didn't know that if you don't set `Access-control-allow-origin` to `*`, you also have to set `access-control-allow-header` and `access-control-allow-method`
        1. Since the large-image caching that was the reason for adopting it was no longer needed, I dropped it with tears in my eyes. I plan to use it in the next project if needed
1. Cypress
    1. Why I chose it
        1. Beta support for `Safari` has started
        2. The existence of `Cypress studio`
    2. Thoughts: too busy to really try it out

20240402
