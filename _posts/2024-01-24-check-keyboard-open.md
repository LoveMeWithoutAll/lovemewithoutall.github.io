---
layout: single
title: Vue에서 모바일 기기의 소프트 키보드가 열려있는지 확인하는 방법
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
  const minKeyboardHeight = 300; // 일반적인 최소 키보드 높이 (px)

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


자꾸 걸리적 거려서 Vue2 mixin으로 만들었다. React 등에서도 응용하여 활용 가능하다.

20240124
