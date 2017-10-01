---
layout: single
title: jekyll을 이용한 블로그 구축기
date: 2017-10-01 23:07:30.000000000 +09:00
type: post
header:
  teaser: "https://jekyllrb.com/img/logo-2x.png"
categories:
- IT
tags: [jekyll]
---

## 0. 시작
블로깅을 재개하기로 결심했다. 오래된 나태함과 타성을 조금이나마 다잡기 위함이다. 
블로깅의 본질이라면 마땅히 글쓰기이겠지만, 나라는 사람은 어쩔 수 없는지라 시작부터 수단에 집착하고야 말았다.
바로 적당한 블로깅 도구를 찾아 구축하는 일이었는데, 이 단계에서부터 생각외로 많은 노력을 투입하느라 일을 지체하고 말았다.
하지만 개발자로서 작은 깨달음도 있었으니 마냥 시간 낭비만은 아니었으리라 자위하고 있다.
지금부터 내가 겪은 고민의 과정을 간단히 적고자 한다. 누군가에게 작으나마 뜻밖의 도움이 되었으면 좋겠다.

## 1. 도구 선택
지금껏 여러 블로깅 도구를 전전해왔다. [Blogger], [egloos], [WordPress.com], 심지어 [tumblr]까지... 마지막으로 열었던 [WordPress.com]은 플러그인 간의 충돌이라는 문제가 마음에 들지 않았다. 다행히 선택권은 많았다. 제각기 장단점이 있어 우열을 가릴 수 없었다. 결국 나의 취향과 필요에 따라 결정했다.
- [WordPress] : 부득이한 경우가 아니라면 php를 새로 배우고 싶지 않았다.    
- [Medium] & [brunch]: 아름다운 디자인과 탁월한 전파력이 강점. 하지만 자유도에 제한이 있어 기각.   
- [JHipster] : SI하듯이 개인 홈페이지를 만들면 어떨까 싶었는데, JHipster를 쓰면 Spring boot + Angular4 개발환경을 빠르고 구축할 수 있기에 몹시 끌렸다. 하지만 어느 세월에 블로깅을 시작할 수 있을지 장담할 수 없어 훗날을 기약.   
- [jekyll] : 유행은 지났다. 하지만 개발자 친화적이라는 장점이 컸다. 무엇보다 github page로 만들면 요사이 한산했던 나의 github contributions graph를 채워줄 수 있어 좋았다. 

## 2. 구축 및 마이그레이션
첫 구축은 아주 간단했다. [jekyll Quick-start guide]에 나온대로 따라한 후, github repository 하나 만든 다음 git push만 하면 끝이다. windows 사용자는 다소 귀찮은 일을 해야한다는데, 나는 데스크톱은 민트 리눅스요 랩탑은 맥북인지라 쉽게 넘어갔다. 기본 테마지만 깔끔하니 보기 좋다. [WordPress.com]에 있던 예전 블로그의 글을 마이그레이션하는 작업도 놀랄만큼 쉽게 끝냈다. 역시 남들이 많이 쓰는 기술을 따라 쓰면 중간은 간다.

## 3. 테마 선택
2에서 끝내고 본격적인 블로깅에 집중했으면 좋으련만, 나는 말단에 집착하는 가련한 중생인지라 조금이라도 블로그를 예쁘게 꾸밀 방법을 모색하기 시작했다. 다행히도 나같은 사람을 불쌍히 여긴 선행자들이 만들어둔 테마가 있었다. 테마를 적용하면 댓글 등 필수적인 플러그인도 크게 신경쓸 필요없이 알아서 해준다. 테마는 장터([jekyllThemes.io], [jekyllrc theme], [jekyllTheme.org])에서 받을 수 있다. 예쁘고 실용적인 테마들이 많다. [windows95 theme]처럼 재미있는 테마도 있다. 나는 [minimal mistake]로 골랐다. 사용자가 많고, github에 star와 fork가 많고, 업데이트가 충실했기 때문이다. 이번에도 뇌동개발이다.

## 4. 테마 적용
여기서 많은 시행착오가 있었다. 난관 및 해결책은 다음과 같다.
1. 테마 설정: [minimal mistake] 테마의 기능이 다소 많은 나머지 설정해줄 것들이 제법 있었다. 해당 테마의 Quick-start 문서와 예제를 보면서 _config.yml 파일을 한줄 한줄 설정해야 했다.
1. Front Matter: jekyll을 적용하려면 html이든 마크다운이든 문서 첫줄에 front matter라는걸 적어야 한다. 대시 3개(---) 사이에 글의 메타 정보를 담는 부분인데, 잘 맞춰 적지 않으면 jekyll이 빌드를 못한다. jekyll을 사용한 블로깅을 위해 새로 배워야 할 것은 이 뿐이다.
1. ruby, gem: 나는 ruby를 모른다. gem은 더더욱 모른다. 하지만 jekyll은 ruby로 만들어졌고, 이에 따라 생소한 문법을 마주해야 한다. 다행히도 공식 문서에 나온대로 따라하면 대부분의 문제를 해결할 수 있고, 그래도 막히면 구글신이 알려주니 곧 익숙해진다. 그 외에는 적용하려는 테마가 알아서 해준다. 
1. 한글: 테마에 따라 한글이 깨지는 경우가 있다고 한다. 일단 [minimal mistake]가 그렇다. 긴 삽질 후, 속 편하게 영어를 쓰기로 했다. 자고로 컴퓨터와는 이야기 할 때 영어와 숫자, 비트 외의 다른 무언가를 쓰면 피곤해진다.

## 5. Markdown editor 선택
이제 블로그는 갖추어졌으니 에디터를 골라야한다. html로 글을 써도 괜찮지만 굳이 그러지는 않기로 했다. 어짜피 내가 퍼블리셔도 아니고 html을 이쁘게 짜는 감각도 없다. 여기에도 몇가지 선택지가 있었다. 유료 에디터는 고려하지 않았다. 결론은 VisualStudio Code다.
1. WebStorm: 프론트엔드 개발에 늘 쓰던 물건인지라 아무 생각없이 이것부터 돌렸다. 마크다운 미리보기를 지원하는 플러그인을 설치하면 된다. 하지만 글 쓰는데 IDE까지 필요할리 없다는 생각에 기각.
1. VisualStudio Code: 당금 최강의 텍스트 에디터. 당연히도 Markdown을 지원하는 플러그인이 있다. 믿고 쓰면 된다.
1. [stackEdit] : 고오급 개발자의 향기가 나는 에디터. 웹브라우저에서 작동한다. VisualStudio Code조차 stackEdit에는 미치지 못한다. 웹이라는게 유일한 단점이라면 단점.

## 6. 검색엔진 노출
어찌됐든 블로그니만큼 최소한의 자기 현시는 할 필요가 있다. 그러기 위해서는 검색엔진에 노출을 시켜야하는데, github pages는 별도 설정하기 전에는 검색엔진에 노출되지 않는다고 한다. 귀찮지만 한번은 거치고 넘어가야한다. google과 bing은 대부분의 theme에서 지원한다. 사이트를 인증하고 각각의 웹마스터 도구에 들어가 sitemap.xml 정도만 등록해주면 된다. 한국어가 메인인 블로그니만큼 naver와 daum도 등록해줘야 한다. 이것도 가이드대로만 해주면 된다.

## 7. 마무리
개인 홈페이지에 처음으로 관심을 가졌던 건 밀레니엄의 여운이 채 가시지 않았던 서기 2000년이었다. 당시 중학생이던 나는 html이니 서버니 하는 것들을 직접 손대는건 엄두도 내지 못하고 그 당시 기준으로도 꽤나 원시적이었던 나모 웹에디터나 훔칫거려야 했다. 그 후 십 수년이 지나 중급 개발자(노가다 십장)이 된 지금, 시대는 변하여 개인 홈페이지란 고릿적 유물로 전락했고, sns와 블로그(이조차도 상당히 유행이 지났다)의 시대가 되었다. 이제 보잘 것 없으나마 직접 블로그를 구축하여, 오랜 한을 일성이나마 풀었다. 하지만 그 과정은 어릴 적 내가 생각하던 그것과는 판이하게 달랐다. 나는 ruby를 모르면서도 ruby로 빌드하는 블로그를 만들었고, 이는 개발자가 아니라도 큰 어려움 없이 할 수 있는 수준이었다. 예전에 같이 프로젝트를 하던 선배 개발자가 유니티 컨퍼런스를 다녀와서 한 말이 떠오른다. 이제 더이상 언어 하나를 잘해서 개발하던 시대는 끝났다고. 아마도 개발자의 역할은 점점 변할 것이다. 부지런히 따라가는 수 밖에.

[egloos]: <http://egloos.com>
[Medium]: <https://medium.com>
[brunch]: <https://brunch.co.kr>
[WordPress]: <https://wordpress.org>
[jekyll]: <https://jekyllrb.com>
[JHipster]: <http://www.jhipster.tech>
[WordPress.com]: <https://wordpress.com>
[Blogger]: <https://www.blogger.com>
[tumblr]: <https://www.tumblr.com>
[jekyll Quick-start guide]: <https://jekyllrb.com/docs/quickstart>
[minimal mistake]: <https://github.com/mmistakes/minimal-mistakes>
[jekyllThemes.io]: <https://jekyllthemes.io>
[jekyllrc theme]: <http://themes.jekyllrc.org>
[jekyllTheme.org]: <http://jekyllthemes.org>
[windows95 theme]: <http://jekyllthemes.org/themes/windows-95>
[stackedit]: <https://stackedit.io>