---
layout: single
title: My Thoughts on Using element-ui
date: 2018-08-06 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
    image: "https://camo.githubusercontent.com/462f24153b8e8739c8ea71f7102585c4cb0e1575/68747470733a2f2f63646e2e7261776769742e636f6d2f456c656d6546452f656c656d656e742f6465762f656c656d656e745f6c6f676f2e737667"
categories:
- IT
tags: [Vue.js, JavaScript]
---

Last week I built a service using [vuetify]. Fed up (?) with [vuetify]'s clutter (?), I then built a service using [element-ui], which looks comparatively clean, and jotted down my impressions.

## Pros

1. It's pretty.
1. The documentation is remarkably neat and well done.
1. It's as easy to learn as [Bootstrap].

## Cons

1. As of v2.5.4, Korean input doesn't work well in forms when using IE (Internet Explorer) (!). Of course, this can be fixed by modifying [element-ui]'s source code; just refer to [this post](https://lovemewithoutall.github.io/it/element-ui-with-ie/) to fix it.
1. Compared to other UI frameworks like [vuetify], there are a few missing components (such as a divider...).
1. The ecosystem is less rich than [vuetify]'s.
1. Surprisingly, the default language setting is Chinese. So to view the calendar in English, you have to set the language to English via i18n.
1. ~~Unlike [vuetify], you can't install it with a [Vue CLI] command.~~ Now you can install it ([vue add element](https://github.com/ElementUI/vue-cli-plugin-element)).

## Overall

1. I'm not sure it's good enough to have several times as many GitHub stars as other [Material Design]-family UI frameworks.
1. I'd be reluctant to use it in situations where you can't control the web browser. After all, Korean input doesn't work well in IE...

The end!

[Bootstrap]: http://getbootstrap.com
[Material Design]: https://material.io/design/
[vuetify]: https://github.com/vuetifyjs/vuetify
[Vue CLI]: https://cli.vuejs.org/
[element-ui]: https://github.com/ElemeFE/element
[Vue.js]: https://vuejs.org/
