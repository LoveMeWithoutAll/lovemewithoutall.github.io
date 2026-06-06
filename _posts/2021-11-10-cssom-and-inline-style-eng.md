---
layout: single
title: CSSOM and inline-style
date: 2021-11-10 19:14:30.000000000 +09:00
type: post
header:
    teaser: "https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/full-process.png"
    image: "https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/full-process.png"
categories:
- IT
tags: [css]
---

When the HTML parser encounters an inline style, does it pause the parser to build the CSSOM?
The CSSOM is the model JavaScript uses to manipulate CSS styles.
The CSSOM is built every time, whether external CSS is fetched, a style tag is encountered, or an inline style is encountered. It is built before rendering happens and before JS runs.

If the HTML parser is briefly blocked to build the CSSOM every time it encounters an inline-style, wouldn't that hurt performance accordingly?
I can't find a clear answer.

https://stackoverflow.com/questions/64549997/is-cssom-created-only-when-the-html-page-has-link-for-external-sheet-or-is-it-ev

20210927
