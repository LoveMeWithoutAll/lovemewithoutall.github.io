---
layout: single
title: Translating Easily with Sphinx (Updating the Source Document)
date: 2018-03-22 19:07:30.000000000 +09:00
type: post
header:
  teaser: "http://www.sphinx-doc.org/en/stable/_static/sphinxheader.png"
  image: "http://www.sphinx-doc.org/en/stable/_static/sphinxheader.png"
categories:
- IT
tags: [sphinx, python, translation]
---


When you write technical documentation, using [sphinx] is convenient. And if you are translating technical documentation, [sphinx-intl] is extremely useful.
It has many advantages, but it also easily solves the problem that causes the most pain for technical documentation translators: <U>the frequent changes to the source document</U>.
It pinpoints exactly which parts have changed and tells you. You just need to re-translate those parts!

Let's get started.

This is based on translating into Korean. Of course, there is also a [Korean translation](http://docs-korean-sphinx.readthedocs.io/ko/docs-korean/index.html) of the official Sphinx documentation.

1. Update the changes to the document
```bash
# Pull from the original repository of the document
git pull master 
```

2. Update the translation files
```bash
# Update (regenerate) the po file, which is Sphinx's translation file
make gettext
cd docs
sphinx-intl update -p _build/locale -l ko
```

3. Check the updated parts
- Compare carefully with git diff and organize the updated documents
- Searching for `#, fuzzy` makes it easy to find the changed parts of the source text!

4. Check it in HTML
```bash
# Render the page translated into the ko language as HTML
make -e SPHINXOPTS="-D language='ko'" html 
```

[The Hitchhiker's Guide to Python!](http://python-guide-kr.readthedocs.io/ko/latest/) is being translated using the method above. ~~It's doomed~~

[sphinx]: http://www.sphinx-doc.org/en/stable/index.html
[sphinx-intl]: http://www.sphinx-doc.org/en/stable/intl.html
