---
layout: single
title: How to Load a Global Type Defined at the yarn workspace Root from a Sub-Package
date: 2025-04-07 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/2/29/TypeScript_Logo_%28Blue%29.svg/1280px-TypeScript_Logo_%28Blue%29.svg.png"
categories:
- IT
tags: [typescript]
---

## Environment
* typescript v5.8.3
* yarn workspace

## The Symptom
1. Build a monorepo using yarn workspace
1. Set up tsconfig at the workspace root
1. Extend the workspace root's tsconfig in a sub-package
1. <B>The global type defined at the workspace root is not recognized in the sub-package!</b>

## The Cause
This happens because both the workspace root's tsconfig and the sub-package's tsconfig had their own `include` settings.
### The `include` setting is not extended. It is only overridden.
That is why the global type set at the workspace root was not applied to the sub-package.

## The Solution
Set `include` only in the sub-package, as shown below.

```json
{
  "extends": "../../tsconfig.json", // the workspace root's tsconfig
  "files": [],
  "include": ["src", "../../types/**.d.ts"] // the global type is defined at this path
}
```

## How to Debug TypeScript Configuration

There is a way to check how tsconfig is configured in the current directory.

```bash
tsc --showConfig
```

Using the command above, you can check the TypeScript configuration resolved for the current directory. The output looks like the following.

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
