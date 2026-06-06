---
layout: single
title: Error Handling in Next.js Dynamic Routes
date: 2024-10-29 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js]
---

Using Next.js's dynamic route feature, you can easily build an API proxy server. It's a nice feature, but the way you respond with errors from a proxy server is a bit peculiar.

The steps are as follows.

### 1. Catch the error in the route file.
```typescript
// dynamic route file
import nextJsErrorHandler from '@/app/api/proxy/nextJsErrorHandler';
import { NextRequest, NextResponse } from 'next/server';

export const DELETE = async ( req: NextRequest ) => {
  try {
    const response = await apiTask();

    return NextResponse.json(response);
  } catch (error: unknown) {
    return nextJsErrorHandler(error); // When an error occurs in the API, return the error. You must not throw it.
  }
};
```

### 2. The error format you return must be exactly the format specified by Next.js.
```typescript
// A function that turns the error into the json format specified by next.js
import { isApiError } from '@/api/http/HttpErrorHandler';
import { NextResponse } from 'next/server';

const nextJsErrorHandler = (error: unknown) => {
  // It must be in the json format shown below
  // { { error: string, status: number }, { status: number } }
  return NextResponse.json({ error: 'Unknown error', status: 500 }, {
    status: 500,
  } as any);
};

export default nextJsErrorHandler;
```

Only when you build the json in the format above will it be recognized as a valid response. This is a constraint that isn't mentioned in the official docs. A peculiar framework.

20241029
