---
layout: single
title: A Custom Hook for Quickly Changing Numbers (feat. React)
date: 2024-03-26 09:50:30.000000000 +09:00
type: post
header:
    teaser: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9Ii0xMS41IC0xMC4yMzE3NCAyMyAyMC40NjM0OCI+CiAgPHRpdGxlPlJlYWN0IExvZ288L3RpdGxlPgogIDxjaXJjbGUgY3g9IjAiIGN5PSIwIiByPSIyLjA1IiBmaWxsPSIjNjFkYWZiIi8+CiAgPGcgc3Ryb2tlPSIjNjFkYWZiIiBzdHJva2Utd2lkdGg9IjEiIGZpbGw9Im5vbmUiPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIi8+CiAgICA8ZWxsaXBzZSByeD0iMTEiIHJ5PSI0LjIiIHRyYW5zZm9ybT0icm90YXRlKDYwKSIvPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIiB0cmFuc2Zvcm09InJvdGF0ZSgxMjApIi8+CiAgPC9nPgo8L3N2Zz4K"
    image: "data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9Ii0xMS41IC0xMC4yMzE3NCAyMyAyMC40NjM0OCI+CiAgPHRpdGxlPlJlYWN0IExvZ288L3RpdGxlPgogIDxjaXJjbGUgY3g9IjAiIGN5PSIwIiByPSIyLjA1IiBmaWxsPSIjNjFkYWZiIi8+CiAgPGcgc3Ryb2tlPSIjNjFkYWZiIiBzdHJva2Utd2lkdGg9IjEiIGZpbGw9Im5vbmUiPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIi8+CiAgICA8ZWxsaXBzZSByeD0iMTEiIHJ5PSI0LjIiIHRyYW5zZm9ybT0icm90YXRlKDYwKSIvPgogICAgPGVsbGlwc2Ugcng9IjExIiByeT0iNC4yIiB0cmFuc2Zvcm09InJvdGF0ZSgxMjApIi8+CiAgPC9nPgo8L3N2Zz4K"
categories:
- IT
tags: [react]
---

I built a custom hook that quickly changes a number until it reaches a target value.

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
  const [animatedNumber, setAnimatedNumber] = useState(0); // the number to display on screen

  useEffect(() => {
    if (animatedNumber === 0) return setAnimatedNumber(target); // set the initial value
    if (animatedNumber === target) return; // change complete

    // change the number only at a rate visible to the human eye
    requestAnimationFrame(() => {
      const increment = target > animatedNumber ? 1 : -1; // check whether to increase or decrease the number

      setAnimatedNumber((prev) => {
        if (increment) return prev + increment > max ? max : prev + increment; // when increasing, only go up to max
        return prev + increment <= min ? min : prev + increment; // when decreasing, only go down to min
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

{% raw %}
```typescript
const Info = () => {
  const { experiencePercent } = useMainCharacterStore(); // // increase or decrease by the experiencePercent value
  const { animatedNumber } = useRollingNumber({ target: experiencePercent, min: 0, max: 100 }); // the target is max 0, max 100

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
{% endraw %}


20240326
