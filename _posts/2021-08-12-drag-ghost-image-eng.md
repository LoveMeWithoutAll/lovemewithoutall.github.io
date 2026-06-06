---
layout: single
title: The drag ghost image bug in Google Chrome browser
date: 2021-07-30 19:05:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/2021-08-12-drag-ghost-image.png"
    image: "/assets/images/2021-08-12-drag-ghost-image.png"
categories:
- IT
tags: [chrome]
---

1. Symptom
- Affected element: drag ghost image
- Symptom details
	- The size of the ghost image is not limited to the drag target
	- The ghost image includes a raster image located at the bottom of the scrollable container, with a size corresponding to how far the scroll has moved down
- How to reproduce: scroll the scrollable container down to the bottom and then drag
- Browser: Chrome only
- capture
  ![ghost-image](/assets/images/2021-08-12-drag-ghost-image.png)
1. Solution
- Add a style
	- Style added: position: 'sticky' or 'relative'
	- Where the style was added: add the style to the parent element of the draggable list
3. Cause
- Presumed to be a Chrome bug
	- CSS position should not affect the ghost image of a draggable list
4. Open questions
- position: 'sticky'
	- As far as I know, sticky is meaningless unless an offset such as top or left is set
	- Does sticky produce the same effect as relative when no offset is set?
	- This behavior appears to be a bug in Chromium
5. Runtime environment


| Chrome          | 91.0.4472.114 (Official Build) (x86_64)                                                                                        |   |   |   |
|-----------------|---------------------------------------------------------------------------------------------------------------------------|---|---|---|
| Revision        | 4bb19460e8d88c3446b360b0df8fd991fee49c0b-refs/branch-heads/4472@{#1496}                                                   |   |   |   |
| OS              | V8 9.1.269.36                                                                                                             |   |   |   |
| User Agent      | Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36 |   |   |   |
| Command Line    | --no-first-run --disable-fre --no-default-browser-check --flag-switches-begin --flag-switches-end about:blank             |   |   |   |



20210723
