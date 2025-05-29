---
layout: single
title: 라우트-단위 코드 스플리팅 — React Data Router v7 + Vite 조합으로 “진짜” Lazy Route 만들기
date: 2025-05-29 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://reactrouter.com/splash/hero-3d-logo.dark.webp"
    image: "https://reactrouter.com/splash/hero-3d-logo.dark.webp"
categories:
- IT
tags: [vite, react-router]
---

# 라우트-단위 코드 스플리팅 — React Data Router v7 + Vite 조합으로 “진짜” Lazy Route 만들기

예시 코드는 Monorepo 프로젝트지만, 이번 최적화는 저장소 구조와 무관 하게 적용됩니다. Yarn Workspaces나 Turborepo가 아니더라도 동일한 방법으로 chunk split을 구현할 수 있습니다.

## 환경
- Vite v6.3.5
- react-router v7.6.0

## 1. 왜 또 “라우트-단위” 인가?

초기 번들에 전 페이지가 다 들어가면 첫 페인트까지 JS 다운로드/파싱/실행 시간이 기하급수로 늘어납니다.

React Router v7(이하 Data Router)는 lazy API로 라우트 정의 자체를 동적 import() 할 수 있게 만들었고, Vite는 import() 경계를 기준으로 자동으로 Rollup 청크를 쪼갭니다. 따라서
1.	URL 매칭 ➡︎ 해당 라우트가 처음 필요해질 때
2.	Vite가 만든 청크 파일이 네트워크로 내려오고
3.	React 는 받은 모듈에서 Component/loader/action 같은 속성만 추출해 바로 렌더링합니다.

참고: [Faster Lazy Loading in React Router v7.5+
](https://remix.run/blog/faster-lazy-loading)

------

## 2. 구현 코드 한눈에 보기

```tsx
// App.tsx — 필요한 라우트만 가져오는 Data Router 설정
const routes = [
  { element: <Layout/>,
      children: [
        { path: '/', element: <DashboardLayout/>,
          children: [
            { index: true, element: <Dashboard/> }
          ]
        },

      // 🟢 /lazy 노선 예시 — Notice Board
      { path: '/notice',
        lazy: () => import('@mso/notice-board/src/notice-board.ts')
                     .then(m => ({ Component: m.NoticeBoardLayout })),
        children: [
          { index: true,
            lazy: () => import('@mso/notice-board/src/notice-board.ts')
                         .then(m => ({ Component: m.NoticeBoard })) },
          { path: ':noticeNo',
            lazy: () => import('@mso/notice-board/src/notice-board.ts')
                         .then(m => ({ Component: m.NoticeContent })) },
        ],
      },

      // 🟢 /qna 도 같은 방식
      { path: '/qna',
        lazy: () => import('@mso/qna/src/qna.ts')
                     .then(m => ({ Component: m.QnaLayout })),
        children: [
          { index: true,
            lazy: () => import('@mso/qna/src/qna.ts')
                         .then(m => ({ Component: m.QnA })),
            loader: () => showFooter(),
          },
          { path: 'answer',
            lazy: () => import('@mso/qna/src/qna.ts')
                         .then(m => ({ Component: m.Answer })),
            loader: () => hideFooter(),
          },
          { path: 'complaint',
            lazy: () => import('@mso/qna/src/qna.ts')
                         .then(m => ({ Component: m.Complaint })),
            loader: () => hideFooter(),
          },
        ],
      },
  ]},
];
```

* 패키지명이든 상대 경로든 상관없습니다. 중요한 것은 “동적 import() 경계” 가 생긴다는 사실뿐입니다.
* 각 패키지에는 NoticeBoardLayout, QnA 같은 배럴 파일(src/notice-board.ts, src/qna.ts)을 두어 “진입점 하나 = 1 청크” 로 묶었습니다.

------

## 3. Vite 측 설정 – modulePreload OFF

Vite는 기본적으로 청크를 만들고도 index.html에 <link rel="modulepreload">를 삽입해 선제 다운로드를 시도합니다. 초기 화면에 필요 없는 청크까지 받아버리면 Lazy Route의 의미가 퇴색하겠죠.

해결책은 매우 간단 합니다.


```ts
// vite.config.ts
export default defineConfig({
  // …plugins, server 설정 등
  build: {
    modulePreload: false,   // preload 태그 싹 제거
  },
});
```

build.modulePreload:false 값을 주면 빌드 결과물에서 모든 preload 링크가 빠지고, 브라우저는 실제 import()가 실행되기 전까지 청크를 요청하지 않습니다.  ￼ ￼

### 참고
* [Disable preload in Vite](https://stackoverflow.com/questions/70980498/disable-preload-in-vite/70995537)
* [vite 공식 문서 build.modulePreload](https://vite.dev/config/build-options#build-modulepreload)

### 검증 팁
  1.	프로덕션 빌드 후 Network 탭에서 /notice 진입 전에는 notice-*.js 가 보이지 않는지 확인
  2.	해당 경로로 이동한 순간에만 해당 청크가 Initiator=script 로 로드되는지 확인

-------

## 4. FAQ & 자주 만나는 시행착오

|증상|원인/대응|
|---|---|
|children 속성을 lazy() 결과에 넣었더니 TS 에러|lazy 함수는 Component/loader/action/ErrorBoundary만 반환해야 합니다. 경로 매칭 속성(children, path)은 부모 RouteObject에 그대로 둬야 합니다.|
|notice-*.js 대신 index-*.js 청크가 생김 | 배럴 파일 이름이 index.ts 면 Rollup이 기본 이름을 index로 잡습니다. manualChunks() 혹은 lib.fileName 옵션으로 원하는 접두어(notice-board)를 지정하면 해결됩니다.|
| react-*.js 같은 의외의 vendor 청크가 추가 로드됨| 여러 Lazy Route가 공유하는 node_modules(예: zustand/react) 라서 Vite의 splitVendorChunkPlugin 이 자동으로 분리한 것입니다. 성능상 이득이므로 그대로 두는 것을 권장합니다.|

------

## 5. 결론

이번 예시는 Yarn Workspaces monorepo에서 나온 실전 코드이지만,

사실상 핵심은 두 가지 뿐입니다.

1.	Data Router의 lazy() 로 라우트 모듈을 동적 import() 한다.
2.	Vite build.modulePreload:false 로 초기 preload 를 막아 “정말로 라우트 진입 시점에만” 다운로드하도록 한다.

폴더 구조가 하나의 레포인지, 여러 패키지를 workspace로 묶었는지는 전혀 영향을 주지 않습니다.
SPA 용량을 확 줄이고 싶은 분이라면, 모듈 경계만 import()로 끊어 주고 위 옵션만 켜보세요. 처음 열리는 화면은 더 가벼워지고, 사용자가 실제로 클릭한 페이지는 지연 없이 스무스하게 로드되는 경험을 바로 얻을 수 있습니다.


-------

EOD

20250529
