---
layout: single
title: "Route-Level Code Splitting — Building \"Real\" Lazy Routes with React Data Router v7 + Vite"
date: 2025-05-29 10:49:30.000000000 +09:00
type: post
header:
    teaser: "https://reactrouter.com/splash/hero-3d-logo.dark.webp"
    image: "https://reactrouter.com/splash/hero-3d-logo.dark.webp"
categories:
- IT
tags: [vite, react-router]
---

# Route-Level Code Splitting — Building "Real" Lazy Routes with React Data Router v7 + Vite

The example code is from a Monorepo project, but this optimization applies regardless of repository structure. Even if you're not using Yarn Workspaces or Turborepo, you can implement chunk splitting the same way.

## Environment
- Vite v6.3.5
- react-router v7.6.0

## 1. Why "Route-Level" Again?

When every page is bundled into the initial bundle, the time to download/parse/execute the JS before the first paint grows exponentially.

React Router v7 (hereafter Data Router) made it possible to dynamically `import()` the route definitions themselves via the lazy API, and Vite automatically splits Rollup chunks at the `import()` boundaries. As a result:
1.	URL matches ➡︎ when a given route is first needed
2.	the chunk file Vite created comes down over the network, and
3.	React extracts only properties like Component/loader/action from the received module and renders right away.

Reference: [Faster Lazy Loading in React Router v7.5+
](https://remix.run/blog/faster-lazy-loading)

------

## 2. The Implementation Code at a Glance

```tsx
// App.tsx — Data Router config that imports only the routes you need
const routes = [
  { element: <Layout/>,
      children: [
        { path: '/', element: <DashboardLayout/>,
          children: [
            { index: true, element: <Dashboard/> }
          ]
        },

      // 🟢 /lazy route example — Notice Board
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

      // 🟢 /qna works the same way
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

* It doesn't matter whether you use a package name or a relative path. The only thing that matters is that a "dynamic `import()` boundary" is created.
* Each package has a barrel file (src/notice-board.ts, src/qna.ts) that groups exports like NoticeBoardLayout and QnA, bundling things so that "one entry point = one chunk."

------

## 3. The Vite-Side Setup – modulePreload OFF

By default, even after creating chunks, Vite inserts `<link rel="modulepreload">` into index.html to attempt preemptive downloads. If you end up downloading even the chunks you don't need for the initial screen, the whole point of Lazy Routes is lost.

The solution is very simple.


```ts
// vite.config.ts
export default defineConfig({
  // …plugins, server settings, etc.
  build: {
    modulePreload: false,   // strip out all preload tags
  },
});
```

Setting `build.modulePreload: false` removes every preload link from the build output, and the browser won't request the chunks until the actual `import()` runs.  ￼ ￼

### References
* [Disable preload in Vite](https://stackoverflow.com/questions/70980498/disable-preload-in-vite/70995537)
* [Vite official docs: build.modulePreload](https://vite.dev/config/build-options#build-modulepreload)

### Verification Tips
  1.	After a production build, check the Network tab to confirm that notice-*.js does not appear before you enter /notice
  2.	Confirm that the chunk loads with Initiator=script only at the moment you navigate to that route

-------

## 4. FAQ & Common Pitfalls

|Symptom|Cause/Fix|
|---|---|
|Putting the children property in the lazy() result throws a TS error|The lazy function should only return Component/loader/action/ErrorBoundary. Route-matching properties (children, path) must stay on the parent RouteObject.|
|An index-*.js chunk is created instead of notice-*.js | If the barrel file is named index.ts, Rollup defaults its name to index. You can fix this by specifying the desired prefix (notice-board) via the manualChunks() or lib.fileName option.|
| An unexpected vendor chunk like react-*.js gets loaded additionally| It's a node_modules dependency (e.g. zustand/react) shared by several Lazy Routes, so Vite's splitVendorChunkPlugin separated it automatically. It's a performance win, so we recommend leaving it as is.|

------

## 5. Conclusion

This example came from real-world code in a Yarn Workspaces monorepo,

but in practice there are really only two key points.

1.	Use Data Router's lazy() to dynamically import() the route modules.
2.	Use Vite's build.modulePreload:false to block the initial preload so things download "truly only at the moment you enter the route."

Whether your folder structure is a single repo or several packages bundled into a workspace makes no difference at all.
If you want to dramatically reduce your SPA's size, just break the module boundaries with import() and turn on the option above. The first screen that opens will be lighter, and you'll immediately get the experience where the pages users actually click load smoothly without delay.


-------

EOD

20250529
