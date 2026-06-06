---
layout: single
title: How to Check Whether the Soft Keyboard on a Mobile Device Is Open in Vue
date: 2024-01-24 09:50:30.000000000 +09:00
type: post
header:
    teaser: "https://learn.microsoft.com/ko-kr/xamarin/xamarin-forms/platform/android/soft-keyboard-input-mode-images/pan-resize.png"
    image: "https://learn.microsoft.com/ko-kr/xamarin/xamarin-forms/platform/android/soft-keyboard-input-mode-images/pan-resize.png"
categories:
- IT
tags: [javascript]
---

```javascript
<script>
const detectKeyboardOpen = () => {
  const minKeyboardHeight = 300; // Typical minimum keyboard height (px)

  return window.screen.height - minKeyboardHeight > window.visualViewport.height;
};

export default {
  name: 'MobileKeyBoardChecker',
  data() {
    return {
      isKeyboardOpen: false,
    };
  },
  created() {
    window.visualViewport.addEventListener('resize', this.checkKeyboardOpen);
  },
  beforeDestroy() {
    window.visualViewport.removeEventListener('resize', this.checkKeyboardOpen);
  },
  methods: {
    checkKeyboardOpen() {
      this.isKeyboardOpen = detectKeyboardOpen();
    },
  },
};
</script>
```


It kept getting in the way, so I turned it into a Vue2 mixin. You can also adapt and use it in React and elsewhere.

20240124
