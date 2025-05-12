---
layout: single
title: Vitest 환경에서도 .env 환경변수를 쓰고 싶다면?
date: 2025-05-12 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://vitest.dev/logo-shadow.svg"
    image: "https://vitest.dev/logo-shadow.svg"
categories:
- IT
tags: [vitest]
---

# Vitest 환경에서도 .env 환경변수를 쓰고 싶다면?

## 환경
- vitest 3

테스트를 돌릴 때마다 import.meta.env.VITE_API_URL 같은 값이 undefined로 찍혀서 당황한 적 있으신가요?
프로덕션 빌드나 vite dev에서는 잘 들어오던 .env 값들이 Vitest만 돌리면 사라지는 이유, 그리고 단 한 줄로 해결하는 방법을 정리해 봤습니다.

## 1. 왜 Vitest에서는 env가 비어 있을까?

Vite는 모드(--mode)를 기준으로 다음 규칙에 따라 .env.* 파일을 읽어 정적으로 치환합니다.

[Vite 환경변수 우선순위 멘탈 모델
](https://lovemewithoutall.github.io/it/vite-env-mental-model/)

웹팩 시절의 process.env처럼 런타임이 아니라 빌드 타임에 문자열로 치환되기 때문에,
테스트 실행 시점에 지정한 mode가 다르면 원하는 파일이 로드되지 않습니다.

### 즉, 테스트도 실제 서비스와 같은 모드로 돌려야 import.meta.env가 채워집니다.


## 2. 해결책: vitest run --mode [mode]

편의상 mode는 `local-proxy`로 설정했습니다.

```bash
# CLI
# mode = local-proxy
vitest run --mode local-proxy        # 일회성 실행
vitest watch --mode local-proxy      # 워치 모드
```

단순하지만 강력합니다.

`--mode local-proxy`를 주면 Vite·Vitest가 `.env.local-proxy`(및 .env.local-proxy.local)를 읽고,
모든 import.meta.env.* 상수를 정적으로 치환해 줍니다.

### package.json에 스크립트로 고정

```json
{
  "scripts": {
    "test": "vitest run --mode local-proxy",
    "test:watch": "vitest watch --mode local-proxy"
  }
}
```

CI에서 다른 모드(production 등)로 테스트하고 싶을 땐 스크립트를 추가로 작성하거나
npm run test -- --mode production처럼 인라인 인수로 덮어쓰면 됩니다.

## 3. 실전 예제

### 1) .env.local-proxy

```
VITE_HOME_URL=/
```

Vite는 기본적으로 VITE_ 프리픽스가 붙은 항목만 클라이언트 코드에 노출합니다.
서버 전용 변수(process.env로만 쓰는 값)는 프리픽스를 제거하거나 다른 방식으로 관리하세요.

### 2) Logo.tsx

```tsx
const Logo = () => (
  <a className="c-icon c-icon--11st" href={import.meta.env.VITE_HOME_URL}>
    11번가
  </a>
);
```

### 3) Vitest 실행

```bash
yarn test        # package.json에 위 스크립트를 넣었다면
# 내부적으로 vitest run --mode local-proxy 실행
```

출력되는 DOM:

```html
<a class="c-icon c-icon--11st" href="/">11번가</a>
```


테스트 코드:

```ts
import { render, screen } from '@testing-library/react';

it('링크 href가 올바르게 주입된다', () => {
  render(<Logo />);
  expect(screen.getByRole('link', { name: /11번가/ })).toHaveAttribute('href', '/');
});
```

PASS ✅

더 이상 href가 undefined거나 #가 아니므로 접근성 검사도 통과합니다.

## 4. 자주 묻는 질문(FAQ)

### Q1. .env.test를 쓰면 안 되나요?

A. 가능합니다. 다만 모드를 바꾸지 않고 envDir 옵션으로 경로를 지정하거나,
loadEnv()로 직접 주입해야 합니다. 간단한 프로젝트라면 --mode 한 줄이 훨씬 편합니다.

### Q2. process.env만 쓰는데도 --mode가 필요할까요?

A. 필요 없습니다. 빌드 타임 치환 대상이 아니므로 dotenv.config()로 런타임에 불러와도 됩니다.
하지만 클라이언트 코드(React 컴포넌트) 에 노출해야 한다면 반드시 import.meta.env를 사용하세요.

### Q3. envPrefix를 커스텀으로 바꾼 경우는?

A. vite.config.ts에서 envPrefix를 변경했다면 테스트도 동일하게 설정되어야 합니다.
그렇지 않으면 변수 이름이 달라져 치환이 실패합니다.

-------

## 5. 마무리

### 요약
	1.	Vite · Vitest는 모드에 따라 .env.* 파일을 정적으로 치환한다.
	2.	테스트에서도 동일한 모드를 지정해야 import.meta.env가 채워진다.
	3.	vitest run --mode local-proxy 단 한 줄이면 끝!

모드를 맞추는 것만으로도 대부분의 문제가 해결됩니다!

gpt가 반 이상 써줬다.

EOD

20250512
