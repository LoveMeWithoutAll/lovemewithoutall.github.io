---
layout: single
title: L4 로드밸런서 + Nginx 환경에서 `trailing slash` 리디렉션이 HTTP 로 떨어지는 이슈와 해결법
date: 2025-06-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://nginx.org/img/nginx_logo_dark.png"
    image: "https://nginx.org/img/nginx_logo_dark.png"
categories:
- IT
tags: [http, nginx]
---

# L4 로드밸런서 + Nginx 환경에서 trailing slash 리디렉션이 HTTP 로 떨어지는 이슈와 해결법

**요약**

L4 로드밸런서가 TLS를 종료하고 Nginx는 평문 HTTP로만 요청을 받는 구조에서,
`absolute_redirect off;` 한 줄로 HTTPS ↔ HTTP 섞임 + 404 문제를 깨끗이 잡았다.

핵심은 "`Location` 헤더가 상대 경로로 적혀있다면, 브라우저가 현재 스킴(HTTPS)을 계승하도록 RFC에 규정돼 있다"는 사실이다.

---

## 1. 서비스 아키텍처

```
Client (HTTPS)
      │
┌───────────────┐
│  L4  Load     │  ← TLS termination
│  Balancer     │
└───────────────┘
      │  (HTTP)
┌───────────────┐
│     Nginx     │  ← 평문 HTTP 수신
└───────────────┘
```

* **TLS 검증**: L4(Layer-4) 장비가 담당
* **Nginx**: 80 포트에서만 평문 HTTP 를 수신
* **서비스 요구**: **모든 클라이언트는 반드시 HTTPS** 로 접근해야 한다.

---

## 2. 재현 현상

1. 사용자가 `https://domain/path`(뒤에 `/` 없음)을 요청
2. Nginx 가 디렉터리로 간주 → 301 리디렉션 출력
3. **`Location: http://domain/path/`**
4. 브라우저가 HTTP 로 재요청 → L4 는 TLS 없음 → 백엔드가 특정 리소스를 못 찾고 **404**
5. 사용자는 "HTTPS 페이지가 왜 갑자기 깨져?" 당황

---

## 3. 원인 파헤치기

### 3-1. 왜 `http://`가 찍혔나?

* L4 → Nginx 구간은 **평문 HTTP**
* Nginx 내부 변수 `$scheme` 값은 **항상 `http`**
* `trailing slash` 자동 리디렉션 시 Nginx 는 절대 URL =`$scheme://$host/...` 로 만든다
  → 결과적으로 **`http://`** 가 박힘

### 3-2. 404 까지 나오는 이유

* 우리 서비스는 HTTP 엔드포인트가 없음(혹은 WAF 차단)
* 브라우저가 HTTP 로 다시 요청 → 404 또는 403

---

## 4. 해결책 ― 한 줄 설정

```nginx
# nginx-vhosts.conf 또는 server 블록에
absolute_redirect off;
```

* Nginx 가 리디렉션 `Location` 값을 **상대 경로**(`/pages/product-reg/`) 로만 내보내도록 강제
* `$scheme` 등을 아예 쓰지 않으니 **http/https 혼동 방지**

---

## 5. “왜 이게 먹히지?” — RFC 7231 때문

> **RFC 7231 §7.1.2 Location**
> “`Location` 값이 상대 URI 형식이면, 최종 값은 **요청 URI effective request URI** 를 기준으로 해석한다.”

즉,

* 클라이언트(브라우저)가 상대 `Location` (`/path/`) 를 받으면
  **“내가 방금 보낸 URL 이 `https://…`였으니까, 거기에 그대로 붙이면 되겠군.”**
* 그래서 HTTPS 스킴과 호스트를 **클라이언트가 자동 계승**
* 결과 리디렉션: `https://domain/path/`
* curl로 테스트 해보아도 마찬가지

---

## 6. 변경 전후 패킷 흐름 요약

| 단계           | 변경 전                 | 변경 후           |
| ------------ | -------------------- | -------------- |
| 클라이언트 → L4   | HTTPS                | HTTPS          |
| L4 → Nginx   | HTTP                 | HTTP           |
| Nginx 301 응답 | `Location: http://…` | `Location: /…` |
| 브라우저 재요청     | HTTP (↓ downgrade)   | HTTPS (스킴 계승)  |
| 최종 응답        | 404                  | 200            |

---

## 7. 실전 팁

1. **로컬/Dev 환경**
   * HTTPS 가 없더라도 상대 `Location` 은 문제 없음

2. **다중 도메인**
   * `absolute_redirect off;` 는 호스트 별 인증서 교체 없이도 안전

---

## 8. 마무리 — 한 줄 요약

**L4-TLS termination 환경에서 Nginx 의 `$scheme` 오판으로 생긴 HTTPS→HTTP 리디렉션은
`absolute_redirect off;` 로 상대 `Location` 헤더를 강제해 깔끔히 해결할 수 있다.**

표준(RFC 7231)이 이미 브라우저 쪽 “프로토콜·도메인 계승” 행동을 보장하니 안심하고 써도 된다.

---

### 참고 자료

* **RFC 7231** – [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content](https://datatracker.ietf.org/doc/html/rfc7231#section-7.1.2)
* **RFC 3986** – Uniform Resource Identifier (URI): Generic Syntax
* Nginx 문서 – [`absolute_redirect`](https://nginx.org/en/docs/http/ngx_http_core_module.html#absolute_redirect)


-----

EOD

20250604
