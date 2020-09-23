---
layout: single
title: Test Vuex mapGetters in modules with Jest
date: 2020-09-23 08:23:30.000000000 +09:00
type: post
header:
    teaser: "https://jestjs.io/img/jest.svg"
    image: "https://jestjs.io/img/jest.svg"
categories:
- IT
tags: [JavaScript, Jest, Vue.js]
---

Let's test mapGetters in modules!

```html
<!-- Vue template -->
<template>
    <span id="period">{{ getOpenPeriodRange() }}</span>
</template>

<script>
import { mapGetters } from "vuex";

export default {
  name: "componentName",
  methods: {
    ...mapGetters("Admin", ["getOpenPeriodRange"])
  }
};
</script>
```

Mocking Vuex with modules makes object so deep! But you have to endure.

```javascript
// spec.js
import { shallowMount } from "@vue/test-utils";
import componentName from "@/components/componentName";

describe("period info", () => {
  const wrapper = shallowMount(componentName, {
    mocks: {
      $store: { // mocking vuex
        modules: {
          Admin: { // module's name is Admin
            getters: { // mocking getters
              getOpenPeriodRange: "2020-09-01~2020-10-02"
            }
          }
        }
      }
    }
  });

  it("render set open period", async () => {
    const period = wrapper.vm.$store.modules.Admin.getters.getOpenPeriodRange; // retrieve getters
    expect(wrapper.find("#period").text()).toEqual(period); // test
  });
});
```

It works perfectly for me.