---
layout: single
title: yarn workspace의 root에 설정한 global type을 하위 Package에서 불러오는 방법
date: 2025-04-07 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
categories:
- IT
tags: [typescript]
---

## 환경
* typescript v5.8.3
* yarn workspace

## 현상
1. yarn workspace를 사용하여 모노레포를 구축
1. workspace root에서 tsconfig를 설정
1. 하위 package에서 workspace root의 tsconfig를 extend
1. <B>workspace root에서 정의한 global type을 하위 패키지에서 인식하지 못 함!</b>

## 원인
workspace root의 tsconfig와 하위 패키지의 tsconfig에 각각 `include` 설정을 두었기 때문이다. 
### `include` 설정은 extend를 하지 않는다. override만 한다.
그래서 workspace Root에 설정한 global type이 하위 패키지에 적용되지 않은 것이다.

## 해결법
아래와 같이 하위 패키지에서만 `include`를 한다.

```json
{
  "extends": "../../tsconfig.json", // workspace root의 tsconfig
  "files": [],
  "include": ["src", "../../types/**.d.ts"] // 이 path에 global type을 설정했다
}
```

## typescript 설정 디버깅 방법

현재 경로에서 tsconfig가 어떻게 설정되어 있는지 확인하는 방법이 있다.

```bash
tsc --showConfig
```

위 명령을 사용하여 현재 경로에서 설정된 typescript 설정을 확인할 수 있다. 아래와 같은 output이 나온다.

```bash
# Output of tsc --showConfig
{
    "compilerOptions": {
        "tsBuildInfoFile": "../../node_modules/.tmp/tsconfig.app.tsbuildinfo",
        "target": "es2020",
        "useDefineForClassFields": true,
        "lib": [
            "es2020",
            "dom",
            "dom.iterable"
        ],
        "module": "esnext",
        "skipLibCheck": true,
        "composite": true,
        "emitDeclarationOnly": true,
        "moduleResolution": "bundler",
        "allowImportingTsExtensions": true,
        "isolatedModules": true,
        "moduleDetection": "force",
        "noEmit": true,
        "jsx": "react-jsx",
        "strict": true,
        "noUnusedLocals": true,
        "noUnusedParameters": true,
        "noFallthroughCasesInSwitch": true,
        "noUncheckedSideEffectImports": true,
        "allowSyntheticDefaultImports": true,
        "resolvePackageJsonExports": true,
        "resolvePackageJsonImports": true,
        "resolveJsonModule": true,
        "declaration": true,
        "preserveConstEnums": true,
        "incremental": true,
        "noImplicitAny": true,
        "noImplicitThis": true,
        "strictNullChecks": true,
        "strictFunctionTypes": true,
        "strictBindCallApply": true,
        "strictPropertyInitialization": true,
        "strictBuiltinIteratorReturn": true,
        "alwaysStrict": true,
        "useUnknownInCatchVariables": true
    },
    "files": [
        "./src/Header.tsx",
        "./src/Title.tsx",
        "./src/main.tsx",
        "./src/vite-env.d.ts",
        "../../types/global.d.ts"
    ],
    "include": [
        "src",
        "../../types/**.d.ts"
    ]
}
```

EOD

20250407
