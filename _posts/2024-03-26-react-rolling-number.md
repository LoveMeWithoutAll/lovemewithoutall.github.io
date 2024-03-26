---
layout: single
title: 숫자를 빠르게 변경시키는 커스텀 훅(feat. React)
date: 2024-03-26 09:50:30.000000000 +09:00
type: post
header:
    teaser: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9Ii0xMS41IC0xMC4yMzE3NCAyMyAyMC40NjM0OCI+CiAgPHRpdGxlPlJlYWN0IExvZ288L3RpdGxlPgogIDxjaXJjbGUgY3g9IjAiIGN5PSIwIiByPSIyLjA1IiBmaWxsPSIjNjFkYWZiIi8+CiAgPGcgc3Ryb2tlPSIjNjFkYWZiIiBzdHJva2Utd2lkdGg9IjEiIGZpbGw9Im5vbmUiPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIi8+CiAgICA8ZWxsaXBzZSByeD0iMTEiIHJ5PSI0LjIiIHRyYW5zZm9ybT0icm90YXRlKDYwKSIvPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIiB0cmFuc2Zvcm09InJvdGF0ZSgxMjApIi8+CiAgPC9nPgo8L3N2Zz4K"
    image: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9Ii0xMS41IC0xMC4yMzE3NCAyMyAyMC40NjM0OCI+CiAgPHRpdGxlPlJlYWN0IExvZ288L3RpdGxlPgogIDxjaXJjbGUgY3g9IjAiIGN5PSIwIiByPSIyLjA1IiBmaWxsPSIjNjFkYWZiIi8+CiAgPGcgc3Ryb2tlPSIjNjFkYWZiIiBzdHJva2Utd2lkdGg9IjEiIGZpbGw9Im5vbmUiPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIi8+CiAgICA8ZWxsaXBzZSByeD0iMTEiIHJ5PSI0LjIiIHRyYW5zZm9ybT0icm90YXRlKDYwKSIvPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIiB0cmFuc2Zvcm09InJvdGF0ZSgxMjApIi8+CiAgPC9nPgo8L3N2Zz4K"
categories:
- IT
tags: [react]
---

숫자를 목표치까지 빠르게 변경시키는 커스텀 훅을 만들어보았다.

## code

```typescript
import { useEffect, useState } from 'react';

type useRollingNumberProps = {
  target: number;
  min?: number;
  max?: number;
};

const useRollingNumber = ({
  target,
  min = 0,
  max = 100,
}: useRollingNumberProps) => {
  const [animatedNumber, setAnimatedNumber] = useState(0); // 화면에 표시할 숫자

  useEffect(() => {
    if (animatedNumber === 0) return setAnimatedNumber(target); // 초기값 설정
    if (animatedNumber === target) return; // 변경 완료

    // 사람 눈에 보이는 수준으로만 숫자를 변경한다
    requestAnimationFrame(() => {
      const increment = target > animatedNumber ? 1 : -1; // 숫자를 늘릴지 줄일지 확인한다

      setAnimatedNumber((prev) => {
        if (increment) return prev + increment > max ? max : prev + increment; // 숫자를 늘려야 할 경우 max 까지만 늘린다
        return prev + increment <= min ? min : prev + increment; // 숫자를 줄여야 할 경우 min 까지만 줄인다
      });
    });
  }, [target, min, max, animatedNumber]);

  return {
    animatedNumber,
  };
};

export default useRollingNumber;
```

## usage

```typescript
const Info = () => {
  const { experiencePercent } = useMainCharacterStore(); // // experiencePercent 숫자만큼 올리거나 내린다
  const { animatedNumber } = useRollingNumber({ target: experiencePercent, min: 0, max: 100 }); // 목표치는 최대 0, 최대 100

  return (
    <div className='info'>
      <div className='gauge'>
        <span className='graph' style={{ width: `${animatedNumber}%` }} />
        <strong className='percent'>{animatedNumber}%</strong>
      </div>
    </div>
  );
};

export default Info;

```

20240326
