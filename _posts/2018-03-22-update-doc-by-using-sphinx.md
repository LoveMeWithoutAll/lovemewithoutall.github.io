---
layout: single
title: IE에서 Vue.js 사용하기
date: 2018-03-22 19:07:30.000000000 +09:00
type: post
header:
  teaser: "http://www.sphinx-doc.org/en/stable/_static/sphinxheader.png"
  image: "http://www.sphinx-doc.org/en/stable/_static/sphinxheader.png"
categories:
- IT
tags: [sphinx, python, translation]
---


기술 문서를 작성할 때 [sphinx]를 사용하면 편리하다. 기술문서 번역을 한다면 [sphinx-intl]은 아주 유용하다. 
그 장점에는 여러가지가 있지만, 기술 문서 번역자에게 가장 큰 고통을 주는 문제, 바로 <U>원본 문서의 잦은 변경</U> 또한 쉽게 해결해준다. 
변경된 부분만 찍어서 알려준다. 그 부분만 다시 번역하면 된다! 

시작해보자. 

한국어로 번역하는 작업을 기준으로 한다. 물론 스핑크스 공식문서의 [한국어 번역판](http://docs-korean-sphinx.readthedocs.io/ko/docs-korean/index.html)도 있다.

1. 문서 변경 사항 update
```bash
# 문서의 original repository에서 pull을 받는다
git pull master 
```

2. 번역 파일 update
```bash
# 스핑크스의 번역 파일인 po file을 update(재생성)한다
make gettext
cd docs
sphinx-intl update -p _build/locale -l ko
```

3. update 된 부분 확인
- git diff로 잘 비교해서 update된 문서들을 정리한다
- #, fuzzy 로 검색하면 원문의 변경된 부분을 찾기 편리함!

4. html로 확인
```bash
# ko language로 번역된 페이지를 html로 띄운다
make -e SPHINXOPTS="-D language='ko'" html 
```

[파이썬을 여행하는 히치하이커를 위한 안내서!](http://python-guide-kr.readthedocs.io/ko/latest/)는 위 방법으로 번역하고 있다. ~~망했다~~

[sphinx]: http://www.sphinx-doc.org/en/stable/index.html
[sphinx-intl]: http://www.sphinx-doc.org/en/stable/intl.html
