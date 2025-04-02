---
layout: single
title: nginx location 설정을 아무리 다시 해도 404 에러가 뜨던 건에 관하여
date: 2025-04-02 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://nginx.org/img/nginx_logo_dark.png"
    image: "https://nginx.org/img/nginx_logo_dark.png"
categories:
- IT
tags: [nginx]
---

## 상황
* yarn workspace 사용 중
* 각 package 별로 build output 생성하고, js 파일로 배포하여 마이크로 프론트엔드 스타일로 사용하고자 함
* build output 파일 위치 예시: `/docroot/packages/footer/dist/footer.js`
* js 파일 배포 url 예시: `https://도메인/footer/footer.js`
* nginx.conf 파일의 `http.server.location` 설정을 아무리 변경해도 build output 파일에 접근이 안 되며 404 에러가 발생

### 404 에러 발생하는 설정

```nginx
# nginx.conf
http {
  server {
    location /footer {
      root /docroot/packages/footer/dist/; # footer 패키지의 build output인 
      index footer.js; # footer.js를 배포하려던 설정
      try_files $uri $uri/ /footer.js; # 하지만 404 에러가 날 뿐이다
    }
  }
}
```

## 원인
`http.server.location.root`와 `http.server.location.alias` 의 동작을 잘못 이해해서 그렇다. 각 설정의 동작을 알아보자.

### 1. `http.server.location.root`

```nginx
# nginx.conf
http {
  server {
    location /footer {
      root /docroot/packages/footer/dist/; # footer 패키지의 build output인 
      index footer.js; # footer.js를 배포하려던 설정
      try_files $uri $uri/ /footer.js; # 오류가 나면 여기서 찾으라 했지만, 경로가 꼬일 뿐이다
    }
  }
}
```
request가 `https://도메인/footer`로 들어오면, nginx는 실제 경로를 다음과 같이 계산한다.

`root + URI의 나머지 부분 → /docroot/packages/footer/dist/footer`

nginx에서 설정한 root 뒤에, `/footer`를 더 붙여서(`/docroot/packages/footer/dist/footer/footer`) 파일을 찾는다. 그러니 404 에러가 날 수 밖에 없다.

### 2. `http.server.location.alias`

alias를 사용하여 url path 중 `http.server.location`에 적힌 경로를 무시하고, 완전히 다른 디렉토리로 대체하여 매핑할 수 있다.

```nginx
# nginx.conf
http {
  server {
    location /footer {
      alias /docroot/packages/footer/dist/; # 여전히 오류가 발생하는 설정
      index footer.js;
      try_files $uri $uri/ /footer.js; # 여전히 경로는 꼬인다
    }
  }
}
```

alias를 쓰면 해결이 될 줄 알았지만, `도메인/footer` url로 request를 보내면, `301 Moved Permanantly` response 를 받고, 자동으로 `/footer/`로 리다이렉션 되어 404 에러가 발생한다. 301 응답 이후 발생한 request에서는 위에서 설정한 location 설정이 먹히지도 않기 때문에, 서버 어딘가를 찾아 헤매다가 404 에러를 응답한다.

원인은 이렇다. 위와 같이 alias를 쓰고 브라우저에서 `도메인/footer`를 요청하면, 

1.	Nginx는 /footer가 <b>디렉토리라고 판단</b>하고 슬래시(/)를 붙여서 리다이렉션 함 → 301 Moved Permanently 발생
2.	브라우저는 /footer/로 재요청함
3.	`/docroot/package/footer/dist/footer/` 경로는 존재하지 않으므로 404 에러 발생

그렇다면 경로를 명확히 지정하여 해결할 수 있을까?

```nginx
# nginx.conf
http {
  server {
    location /footer/ { # 경로 끝에 /(슬래시) 붙임
      alias /docroot/packages/footer/dist/; # 여전히 오류가 발생하는 설정
      index footer.js;
      try_files $uri $uri/ /footer.js;
    }
  }
}
```

여전히 404 에러가 날 뿐이다. 원인은 nginx에서 alias는 단순히 경로를 바꾸는 것이 아니기 때문이다. url로 들어온 location 경로를 제거한 뒤, alias 대상 경로의 뒤에 붙여서 최종 파일 경로를 만든다.

예를 들어 `요청 URL: 도메인/footer/footer.js`을 전달 받으면, nginx는 `location.alias`의 경로인 `/docroot/packages/footer/dist/` 뒤에 path를 붙여서 `/docroot/packages/footer/dist/footer/footer.js` 파일을 서빙하려고 한다. 해당 경로는 존재하지 않으니 404 오류가 발생한다. 결과적으로 경로 해석상 `footer/footer.js`를 찾으려 하기 때문에, `http.server.location.root`와 같은 문제가 발생한다.

무슨 말인지 모르겠다는 생각이 들지 않는가? 나도 그렇다. 이래서야 문제 해결이 될 수가 없다. 그러니 불필요한 동작을 최대한 줄이고 간결하게 바꿔보자.

## 해결책

단일 파일 배포에 가장 적합한 설정은 다음과 같다.

```nginx
location = /footer/footer.js {
  alias /docroot/packages/footer/dist/footer.js;
}
```

이 설정은 `도메인/footer/footer.js` 요청 하나만 정확하게 처리하므로, 애매한 경로 해석 문제가 사라진다. `request url: 도메인/footer/footer.js`는 정확히 alias의 경로인 `/docroot/packages/footer/dist/footer/footer.js` 파일 하나로 매핑된다. `location =`을 사용하면, nginx는 해당 요청에만 딱 맞는 설정을 적용하고, alias는 전체 경로를 직접 지정하기 때문에 경로 충돌이나 해석 오류의 여지가 없다.

`http.server.location.root`처럼 url path를 nginx의 root 경로 뒤에 붙이지 않고, `http.server.location.alias` 처럼 url path를 nginx의 alias 값으로 대체하지도 않는다.


## 결론

*	`http.server.location.root`는 요청 url 전체를 (nginx에서 설정한)root 경로 뒤에 붙여서 실제 파일 경로를 만든다.
*	`http.server.location.alias`는 요청 경로(url path)에서 (nginx에서 설정한)location 경로와 <b>일치하는 경로를 제거한 후, 나머지를 alias 경로 뒤에 붙인다</b>.
*	슬래시(/) 위치가 매우 중요하며, `location /foo/` 처럼 trailing slash를 포함해야 정확한 매핑이 가능하다.
*	단일 파일이라면 location = 방식이 가장 안정적이다.

멘탈 모델을 만들어보자.

### root
- nginx는 이렇게 root에서 동작한다. "너 브라우저가 /footer/라고 말하면 나는 그걸 /docroot/.../dsit/ 경로에다가 니가 말한 /footer/와 그 뒤에 있는 나머지 말을 그대로 붙일게."
- root: [nginx root] + [url path] 를 서버에서 찾는다.

```
[ Request URL: /static/app.js ]
           ↓
[ location /static/ ]
           ↓
[ root /var/www/ ]
           ↓
[ Result Path: /var/www/static/app.js ]
```

### alias
- nginx는 alias에서 이렇게 동작한다. "너 브라우저가 /footer/라고 말하면 나는 그걸 /docroot/.../dist/로 바꿔서 생각할게. 그리고 /footer/ 뒤에 있는 나머지 말은 그냥 그대로 붙일게."
- alias: [nginx alias] - [nginx location] + [url path] 를 서버에서 찾는다.

```
[ Request URL: /static/app.js ]
           ↓
[ location /static/ ]
           ↓
[ alias /var/www/assets/ ]
           ↓
[ Result Path: /var/www/assets/app.js ]
```

20250402
