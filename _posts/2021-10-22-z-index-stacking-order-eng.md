---
layout: single
title: z-index stacking order
date: 2021-10-22 18:05:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/d/d5/CSS3_logo_and_wordmark.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/d/d5/CSS3_logo_and_wordmark.svg"
categories:
- IT
tags: [css]
---

The CSS property z-index only affects elements that share the same parent. It has nothing to do with elements that have a different parent.
The reason for this is the stacking order. One stacking context is created per element, and the stacking order is applied only within a single stacking element.

---

## Example

The z-index of blue is greater than the z-index of cat. However, because they belong to different stacking contexts, the difference in z-index between them does not affect the stacking order. And the z-index of content, which shares a parent element with cat, is lower than the z-index of cat. The z-index of blue only affects the stacking order within the content element. That is why blue ends up being covered by cat.

https://codesandbox.io/s/z-index-in-blocks-s7box

---

## References

https://www.w3.org/TR/CSS21/visuren.html#layers

https://erwinousy.medium.com/z-index%EA%B0%80-%EB%8F%99%EC%9E%91%ED%95%98%EC%A7%80%EC%95%8A%EB%8A%94-%EC%9D%B4%EC%9C%A0-4%EA%B0%80%EC%A7%80-%EA%B7%B8%EB%A6%AC%EA%B3%A0-%EA%B3%A0%EC%B9%98%EB%8A%94-%EB%B0%A9%EB%B2%95-d5097572b82f

20210915
