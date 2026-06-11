---
layout: single
title: HTML 테이블에서 "일부 컬럼만 가변폭" 만들기 — `min-width`가 안 먹힐 때
date: 2026-06-11 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/a/ab/Official_CSS_Logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/a/ab/Official_CSS_Logo.svg"
categories:
- IT
tags: [css]
---

## 목표

**테이블**의 컬럼 폭 요구사항:

- `A`, `B`, `C`·`D` … 같은 컬럼: **고정폭**
- `긴 컨텐츠가 들어가는 컬럼` **하나만** 요구사항이 복잡했다.
  - 화면이 넓으면 **늘어나서** 남는 공간을 채우고
  - 화면이 좁으면 **최소 250px**를 지킨다.

한 줄 요약: **"나머지는 고정, 특정 컬럼만 가변(최소 250px)"**

---

## 현상

아무 생각없이 style에 `min-width`를 추가했다.

```tsx
<table>
  {/*...*/}
  <col style={{ width: '80px' }} />     // 고정폭 컬럼
  <col style={{ minWidth: '250px' }} /> // 가변 컬럼: 최소 250
  {/*...*/}
</table>
```

하지만 min-width는 동작하지 않았다. 브라우저는 minWidth 를 그냥 무시하고 있었다. maxWidth 도 마찬가지로 무시했다. `table-layout`을 `fixed`로 바꾸든 `auto`로 바꾸든, 내가 무슨 짓을 해도 min max width 는 동작하지 않았다! 대체 왜 이러는 것일까?

---

## 🔍 원인

답은 CSS spec에 있었다.

[10.4 Minimum and maximum widths: 'min-width' and 'max-width'](https://www.w3.org/TR/CSS2/visudet.html#min-max-widths)

를 보면, `table col`의 css로 min-width & max-width가 적용이 될 것처럼 보인다. `non-replaced inline elements, table rows, and row groups`을 제외한 모든 엘리먼트에 적용된다고 하니까.

하지만 `10.4`의 주석을 보면 이렇게 써져 있다.

`In CSS 2.1, the effect of 'min-width' and 'max-width' on tables, inline tables, table cells, table columns, and column groups is undefined.`

table columns에서는 'min-width' and 'max-width'가 undefined 라는 게 뭔데?? CSS spec에서는 'min-width' and 'max-width' 가 어떤 효과를 발생시킬지 정의를 하지 않는다는 것이다. 

무슨 말인지 모를 거지 같은 문서다.

그러니까 브라우저에서 어떻게 style을 구현할지는 알 수가 없다. table col에서 min max width의 사용은 포기해야 한다.

> 참고: [CSS Snapshot 2026](https://www.w3.org/TR/css/)(현행 "CSS"를 구성하는 명세 색인) 기준으로도 **테이블 모델은 여전히 CSS2가 정본**이다 — 이를 대체하는 모던 모듈이 없다. 그래서 (옛 문서처럼 보여도) CSS2를 인용한다. table 버려야 할까.

---

## 해결책

발상을 바꾼다. min/max를 포기한다.

[CSS spec #fixed-table-layout](https://www.w3.org/TR/CSS2/tables.html#fixed-table-layout)을 보면, CSS spec에서 `table-layout` 에 들어가는 값에 따른 동작은 다음과 같다.

| 모드      | 컬럼 폭 결정 방식                                                     | min/max |
|---------|----------------------------------------------------------------|---------|
| `fixed` | `<col>`/첫 행의 **`width`** 로만 결정. width 없는 컬럼끼리는 남는 폭을 **균등 분배** | 무시      |
| `auto`  | 내용 폭 기반(`width:100%`가 슬랙 흡수)                                   | 무시      |

먼저 고정폭 문제부터 해결하자

### 1. 고정폭 문제

1. `table-layout: fixed`로 설정
2. 고정폭 컬럼에는 `width`를 준다
3. 가변폭 컬럼에는 `width`를 주지 않는다 (남는 폭 흡수)

이렇게 하면 우선 고정폭 부여 컬럼의 문제는 해결된다. 가변폭 컬럼은 고정폭 컬럼이 소비하고 남은 테이블의 폭을 모두 차지한다. 따라서 브라우저 창 크기가 넓어지면 컬럼폭도 늘어난다. 

브라우저 창 크기가 좁아지면 가변폭 컬럼의 폭도 줄어든다. 창 크기가 너무 좁으면 가변폭 컬럼의 컨텐츠가 보이지 않는다. 그러니 가변폭 컬럼의 최소폭을 설정해야 한다.


### 2. 가변폭 컬럼의 최소폭 문제

`table col`에는 min-width가 안 먹히니까, `table` 자체에  min-width를 걸어두면 우회할 수 있지 않을까?

**[table 자체 min-width] = [모든 고정폭 컬럼 width의 합] + [가변폭 컬럼의 최소폭]**

으로 걸어두면 되잖아? 

하지만 위에서 확인한 CSS spec에 따르면, table에도 min-width의 효과는 undefined이다. 그러면 이대로 포기해야 하나? 놀랍게도 `table`에서는 min-width가 제대로 동작한다. 크롬-사파리-파이어폭스에서 모두 동작한다. 그러니 계획대로 하면 된다.

코드는 다음과 같다.

```tsx
const tableMinWidth = 고정폭_컬럼의_width_합 + 가변폭_컬럼_최소폭;

<div style={{ overflowX: 'auto' }}> // table이 min-width보다 좁아질 때 스크롤 생김
    <table style={{ 
      tableLayout: 'fixed',    // table-layout: fixed
      minWidth: tableMinWidth, // table min-width
      width: '100%',           // table width가 남는 공간을 100% 흡수
    }}>
      <colgroup>
        <col style={{ width: '80px' }} />     // 고정폭 컬럼
        <col />                               // 가변 컬럼
      </colgroup>
      // ...
    </table>
</div>
```

CSS는 정말 거지 같은 기술이다.

참고로 위 코드는 가변폭 컬럼이 2개 이상이면 깨진다.

---

## 3줄 요약

- html table column의 min & max width style은 무시된다
- 하지만 html table의 min width style은 동작한다
- 따라서 특정 컬럼을 가변폭으로 만들고 싶으면 table에 min-width를 걸어서 우회하자

EOD

20260611
