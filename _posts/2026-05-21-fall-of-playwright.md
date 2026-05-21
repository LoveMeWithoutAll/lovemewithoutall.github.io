---
layout: single
title: 잘 돌아가던 Playwright e2e test가 전부 깨지는 건에 대하여
date: 2026-05-21 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Playwright_Logo.svg/960px-Playwright_Logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Playwright_Logo.svg/960px-Playwright_Logo.svg.png"
categories:
- IT
tags: [.env, playwright]
---

## 환경

- playwright v1.59.1
- vite v8.0.9

## 현상

로컬 환경에서 playwright e2e 테스트가 실패하기 시작했다. 새로 추가한 테스트 코드 뿐만 아니라, 건드리지도 않은 테스트까지 모두 실패했다. 불과 10분 전까지만 해도 잘 돌아가던 테스트였다.

## 원인

playwright 가 참조하는 환경변수에 문제가 있었다.

```ts
// playwright.config.ts
dotenv.config({ path: '.env.test' });
```

위와 같이 playwright에 환경변수를 참조시키고 있었는데, `.env.test` 에서는 백엔드 서버 URL을 이렇게 설정해두고 있었다.

```
// 기존 .env.test
VITE_BACKEND_URL=http://api.test
```

`http://api.test` URL의 서버는 애시당초 존재하지 않았다. 그러면 위 URL 환경변수를 참조하는 모든 테스트는 당연히 실패할 수 밖에 없지 않을까? 당연히 실패했어야 하는 테스트가, 대체 <b>왜</b> 성공하고 있었던 것일까?

이 사태를 이해하려면 먼저 playwright가 무슨 짓을 하고 있었는지 따라가야 한다. 순서대로 살펴보자.

- playwright는 테스트를 실행할 때 자식 프로세스로 로컬 서버를 spawn 해서 테스트를 돌린다. 여기서 로컬 서버라 함은 개발할 때 흔히 말하는 vite 로컬 서버 바로 그거다. 
- playwright는 자식 프로세스(로컬 서버)를 spawn 할 때 `process.env`로 환경변수를 전달한다. `process.env`는 `.env` 등의 파일에서 설정한 모든 환경변수보다 우선순위가 높다.
- 따라서 playwright 는 `dotenv.config({ path: '.env.test' });`에 명시한 대로 `.env.test`를 로컬서버에 `process.env`로 전달한다.
- 로컬 서버는 `VITE_BACKEND_URL=http://api.test`라는 환경변수 때문에 있지도 않은 URL에 API 요청을 보낸다.

그러니까 당연히 테스트가 실패해야 한다는 거 아니야?

### 대체 왜 테스트가 성공(false negative)하는거야?

진정한 원인은 이것이다.

```ts
// playwright.config.ts
webServer: {
  command: 'yarn start',
  url: 'http://localhost:3002',
  reuseExistingServer: !process.env.CI,
},
```

이 설정은 playwright가 로컬 서버를 자식 프로세스로 spawn 하는 설정이다. `reuseExistingServer`에 주목하라. 

### CI환경에 아니라면, 이미 떠 있는 로컬 서버를 그냥 재사용 하자는 설정이다.

그리고 나는 CI 환경이 아니었다.

이제 테스트가 성공할 때와 실패할 때를 나눠보자. 물론 모든 테스트는 다 `pass` 인 상황이다.

- 테스트 성공: 내가 직접 띄운 로컬 서버가 띄워져 있어서 playwright가 새로 로컬 서버를 spawn 하지 않는다. 이 로컬 서버에는 환경변수가 잘 들어가 있었다. 진 그러니 잘못된 환경변수가 주입되지 않는다.
- 테스트 실패: `로컬 서버가 띄워져 있지 않았다.` 그래서 playwright가 새로 로컬 서버를 spawn 했다. 잘못된 환경변수가 들어갔다!

### 그렇다면 왜 환경변수에 요상한 값이 적혀 있던 거야?

`.env.test`는 git에 등록하지 않는 파일이었다. 그래서 내가 제멋대로 값을 넣었던 것이다. 환경변수 파일 관리는 항상 까다롭다.

## 조치

잘못된 환경변수를 수정하니 모든 문제가 해결되었다.

## 1줄 요약

자식 프로세스를 spawn 할 때 `.env` 환경변수를 상속한다는 사실에 대한 무지 + `reuseExistingServer` 에 대한 무지가 결합하여, 테스트가 잘 돌아가다가 깨지는 '비결정적' 오류가 발생했다. `.env` 환경변수 파일은 비결정적 오류를 발생시키니 사용에 주의하자.

EOD

20260521
