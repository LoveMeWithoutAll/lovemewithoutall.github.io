---
layout: single
title: Initial Jest Setup in Vuetify
date: 2019-10-30 15:05:30.000000000 +09:00
type: post
header:
    teaser: "https://jestjs.io/img/jest.svg"
    image: "https://jestjs.io/img/jest.svg"
categories:
- IT
tags: [Vuetify, Vue, Jest]
---

## Environment

* [Vue CLI] v4.0.5
* vue-test-utils v1.0.0-beta.29
* babel-jest v24.9.0

# Error 1

## Symptom

When you `add` [Vuetify] in [Vue CLI] and run `Unit Test`s with [Jest], you get the following error.

```typescript
  console.error node_modules/vue/dist/vue.runtime.common.js:603
    [Vue warn]: Unknown custom element: <v-container> - did you register the component correctly? For recursive components, make sure to provide the "name" option.

    found in

    ---> <Anonymous>
           <Root>
// ...
```

This happens because [Jest] hasn't loaded [Vuetify].

Using [Vuetify] globally fixes the error.

```typescript
// \tests\unit\example.spec.ts
import { shallowMount } from '@vue/test-utils'
import HelloWorld from '@/components/HelloWorld.vue'
import Vue from 'vue'
import Vuetify from 'vuetify'

Vue.use(Vuetify)

describe('HelloWorld.vue', () => {
  it('renders props.msg when passed', () => {
    const msg = 'new message'
    const wrapper = shallowMount(HelloWorld, {
      propsData: { msg },
    })
    expect(wrapper.text()).toMatch(msg)
  })
})
```

<b>However</b>, it's usually more common to use `createLocalVue` like below.

```typescript
import { shallowMount, createLocalVue } from '@vue/test-utils'
import Vuetify from 'vuetify'
import TestComponent from '@/views/TestComponent.vue'

const localVue = createLocalVue()
localVue.use(Vuetify)
```

But this produces the following error.

```typescript
  console.error node_modules/vuetify/dist/vuetify.js:22968
    [Vuetify] Multiple instances of Vue detected
    See https://github.com/vuetifyjs/vuetify/issues/4068

    If you're seeing "$attrs is readonly", it's caused by this
```

## Cause

The cause lies in [Vuetify]. Read this [issue](https://github.com/vuetifyjs/vuetify/issues/4964#issuecomment-477933121).

## Solution

As of 2019.07.04, there's no clean way to solve the problem. That said, [they say they'll fix it down the road](https://github.com/vuetifyjs/vuetify/issues/4964#issuecomment-500574050). So let's try the following.

Create a jest-setup.js file and set up `Vue` and `Vuetify` ahead of time. The file name can be anything. You can place the file wherever is convenient, but personally I recommend putting it under the `/src/tests` path.

```typescript
// jest-setup.js
import Vue from 'vue'
import Vuetify from 'vuetify'

Vue.use(Vuetify)
```

To make the file above run every time you execute a test, add it to your `jest` config file or to `Webpack`'s `jest` property. I recommend adding it to the `jest` config file.

```typescript
// jest.config.js
module.exports = {
  // ...
  setupFilesAfterEnv: ['<rootDir>/tests/jest-setup.js']
  // ...
}  
```

Here, `<rootDir>` is the location where `package.json` resides.

Now if you rerun the tests, the error message no longer appears.

## Miscellaneous

As of 2019.10.28, the information below is no longer needed in `vue-test-utils v1.0.0-beta.29`.

~~If you look at [jest]'s API docs, [`setupTestFrameworkScriptFile` has been deprecated.](https://jestjs.io/docs/en/configuration#setupfilesafterenv-array). But at the time I'm writing this, `vue-test-utils` uses [jest] v23.6.0, so I used [that version's API](https://jestjs.io/docs/en/23.x/configuration#setuptestframeworkscriptfile-string. Later, when [jest]'s version goes up, let's use the `setupFilesAfterEnv` API instead of the deprecated `setupTestFrameworkScriptFile` API.~~

# Error 2

## Symptom

Sometimes you get this error.

```javascript
[Vuetify] Unable to locate target [data-app] in // component name
```

## Cause

When [Vuetify] initializes, it wraps the [Vuetify] components with a `<v-app></v-app>` tag. This tag has `data-app` as an attribute, but when you do unit testing with [Jest], you test on a per-component basis, so you don't declare the `<v-app></v-app>` tag. That's why the error above occurs. Refer to [this link on Stack Overflow](https://stackoverflow.com/questions/51596881/vuetify-issue-with-v-menu/51598858#51598858).

## Solution

Add the following property to the `jest-setup.js` file you created in `Error 1`.

```javascript
document.body.setAttribute('data-app', true)
```

## The End!

[Vuetify]: https://github.com/vuetifyjs/vuetify
[Jest]: https://jestjs.io/
[Vue CLI]: (https://cli.vuejs.org/)
