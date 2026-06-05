---
layout: single
title: New DevTools Feature in Chrome 65 (Local overrides)
date: 2018-03-30 08:07:30.000000000 +09:00
type: post
header:
  teaser: "https://www.google.com/chrome/assets/common/images/chrome_logo_2x.png?mmfb=a5234ae3c4265f687c7fffae2760a907"
  image: "https://www.google.com/chrome/assets/common/images/chrome_logo_2x.png?mmfb=a5234ae3c4265f687c7fffae2760a907"
categories:
- IT
tags: [Chrome, Chrome dev tools, debug, frontend]
---

This is a really great feature, and it's a shame that some people don't know about it, so I'm sharing it.

## [Local overrides](https://developers.google.com/web/updates/2018/01/devtools#overrides)
![Local overrides](https://storage.googleapis.com/webfundamentals-assets/updates/2018/01/overrides.gif)

Now, when you use the *Local overrides* feature, the CSS and document content you edit in the developer tools won't be wiped out even if you press F5. The static sources you edit here are saved to the folder you specify under *Sources > Overrides*.

### Supplementary note
[As of Chrome 66](https://developers.google.com/web/updates/2018/02/devtools#overrides), CSS defined inside the **style** tag of an HTML file is also saved.

## [Changes tab](https://developers.google.com/web/updates/2018/01/devtools#changes)
![Changes tab](https://developers.google.com/web/updates/images/2018/01/changes.png)

It's even better when used together with the Changes tab, because you can see what you've changed.

It's convenient to modify CSS and other files in the developer tools, check the finished source in the *Changes tab*, and then deploy the file saved locally.

Be sure to watch the linked developer tools introduction video too.

## Addendum

Perhaps because they felt it was a shame that so many people don't know about this feature, Google has even created a separate document just to introduce it. Go read the [official Save Changes To Disk With Workspaces document](https://developers.google.com/web/tools/chrome-devtools/workspaces/?hl=ko)!
