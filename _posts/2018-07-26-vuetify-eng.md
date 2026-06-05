---
layout: single
title: My Experience Using Vuetify
date: 2018-07-26 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://cdn.vuetifyjs.com/images/logos/v-alt.svg"
    image: "https://cdn.vuetifyjs.com/images/logos/v-alt.svg"
categories:
- IT
tags: [Vue.js, JavaScript]
---

# The Struggle

Whenever I do frontend development, there's always one thing I feel unsure about: how to make things look nice. The way I see it, my instinctive sense for design isn't great, and even if I were to sit down and properly learn design, just doing the development alone already overwhelms my head. Fortunately, ever since [Bootstrap], there have been plenty of UI component libraries (or frameworks) that produce decent results just by using them as-is. I tend to mainly use [Bootstrap]; it's pretty, but more than anything it's because I'm familiar with it. For some reason, though, I'm also kind of bored (?) with it, and remembering how well-received the [Material Design] things I'd built a few times back in the [AngularJS] days had been, I decided to try building with it.

# The Choice

If you look [here](https://github.com/vuejs/awesome-vue#frameworks) in [awesome-vue], there are a few frameworks that implement [Material Design]. I briefly compared their pros and cons. In conclusion, as the title of this post suggests, [vuetify] was my final choice.

## 1. [vue-material]

### Pros

1. It's the Vue.js version of [AngularJS Material](https://material.angularjs.org/latest/), so if you're familiar with the original, it's easy to use.
1. The official documentation is clean.

### Cons

1. It has everything you'd need, but the number of components is smaller than [vuetify].
1. Its GitHub stars are about half of [vuetify]'s.

## 2. [vuetify]

### Pros

1. It has lots of components.
1. You can get started easily with [Vue CLI 3].
1. It has lots of GitHub stars. More than 12,000.
1. Compared to other frameworks, its ecosystem is relatively rich.

### Cons

1. Maybe because it has too many features, the official documentation is messy.
1. As you develop, the tags and attributes get messy.
1. If you don't start with [Vue CLI 3], you often run into unexplainable, unsolvable errors.

## 3. [muse-ui]

### Pros and Cons

* Its pros and cons are largely the same as [vue-material], so I'll skip them.


# My Impressions of Using It

1. It's good. I plan to keep using it going forward.
1. [vuetify] is harder to use than [Bootstrap]. I suspect it's because there are far more components and far more attributes.
1. The results are well-received. I get the sense that, at some point, people have grown more familiar with [Material Design] and have come to prefer it over [Bootstrap].
1. The HTML tags and attributes get verbose. I wonder if that's just because I'm not using it well.
1. The official documentation seems detailed and well-made, but once I actually used it, it was a bit messy.
1. I also tried [element-ui], which has lots of GitHub stars ([my impressions](https://lovemewithoutall.github.io/it/element-ui/)), but [vuetify] is better.
1. I was amazed by the Chinese power around [Vue.js].

[Bootstrap]: http://getbootstrap.com
[AngularJS]: https://angularjs.org/
[Material Design]: https://material.io/design/
[awesome-vue]: https://github.com/vuejs/awesome-vue
[vue-material]: https://github.com/vuematerial/vue-material
[vuetify]: https://github.com/vuetifyjs/vuetify
[muse-ui]: https://github.com/museui/muse-ui
[Vue CLI 3]: https://cli.vuejs.org/
[element-ui]: https://github.com/ElemeFE/element
[Vue.js]: https://vuejs.org/
