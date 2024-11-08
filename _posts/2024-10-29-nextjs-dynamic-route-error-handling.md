---
layout: single
title: Next.js dynamic route의 에러 처리
date: 2024-10-29 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---

Next.js의 dynamic route 기능을 사용하면 간단하게 API proxy server를 만들 수 있다. 좋은 기능이지만 proxy server에서 오류를 response 하는 방법이 조금 기묘하다.

순서는 다음과 같다.

### 1. route 파일에서 error를 catch한다.
```typescript
// dynamic route 파일
import nextJsErrorHandler from '@/app/api/proxy/nextJsErrorHandler';
import { NextRequest, NextResponse } from 'next/server';

export const DELETE = async ( req: NextRequest ) => {
  try {
    const response = await apiTask();

    return NextResponse.json(response);
  } catch (error: unknown) {
    return nextJsErrorHandler(error); // API에서 오류가 발생하면 에러를 return한다. throw 하면 안 된다.
  }
};
```

### 2. return 하는 에러 포맷은 반드시 Next.js가 지정한 포맷이어야 한다.
```typescript
// error를 next.js가 지정한 json 포맷으로 만들어주는 함수
import { isApiError } from '@/api/http/HttpErrorHandler';
import { NextResponse } from 'next/server';

const nextJsErrorHandler = (error: unknown) => {
  // 반드시 아래와 같은 json 포맷이어야 한다
  // { { error: string, status: number }, { status: number } }
  return NextResponse.json({ error: 'Unknown error', status: 500 }, {
    status: 500,
  } as any);
};

export default nextJsErrorHandler;
```

위 포맷으로 json을 만들어주어야만 정상적인 response로 인식한다. 공식 문서에는 없는 강제성이다. 기묘한 프레임워크다.

20241029
