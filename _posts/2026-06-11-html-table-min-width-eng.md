---
layout: single
title: Making Only Some Columns Flexible-Width in an HTML Table — When `min-width` Just Won't Work
date: 2026-06-11 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/a/ab/Official_CSS_Logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/a/ab/Official_CSS_Logo.svg"
categories:
- IT
tags: [css]
---

## Goal

Column-width requirements for a **table**:

- Columns like `A`, `B`, `C`·`D` …: **fixed width**
- Only **one** column (the one holding long content) had a tricky requirement:
  - When the screen is **wide**, it should **grow** to fill the leftover space
  - When the screen is **narrow**, it should keep a **minimum of 250px**

One-line summary: **"Everything fixed, only one column flexible (min 250px)."**

---

## The Symptom

Without thinking much, I just slapped `min-width` onto the style.

```tsx
<table>
  {/*...*/}
  <col style={{ width: '80px' }} />     // fixed-width column
  <col style={{ minWidth: '250px' }} /> // flexible column: min 250
  {/*...*/}
</table>
```

But `min-width` didn't work. The browser was just ignoring `minWidth`. `maxWidth` was ignored too. Whether I set `table-layout` to `fixed` or `auto`, no matter what I did, min/max width simply would not work! What on earth is going on?

---

## 🔍 The Cause

The answer was in the CSS spec.

[10.4 Minimum and maximum widths: 'min-width' and 'max-width'](https://www.w3.org/TR/CSS2/visudet.html#min-max-widths)

Reading it, it looks like `min-width` & `max-width` *should* apply to a `table col` — after all, it says they apply to every element except `non-replaced inline elements, table rows, and row groups`.

But the note in `10.4` says this:

`In CSS 2.1, the effect of 'min-width' and 'max-width' on tables, inline tables, table cells, table columns, and column groups is undefined.`

What does it even mean that `min-width` and `max-width` are "undefined" for table columns?? It means the CSS spec does **not define** what effect `min-width` and `max-width` will produce.

What a garbage document — you can't even tell what it's trying to say.

So there's no way to know how a browser will implement the style. You have to give up on using min/max width on a table col.

> Note: even by the current [CSS Snapshot 2026](https://www.w3.org/TR/css/) — the index of the specs that together define "CSS" today — **the table model is still defined by CSS2**, with no modern module replacing it. That's why we cite CSS2 here, even though it looks dated. table is terrible.

---

## The Solution

Change the approach. Give up on min/max.

Looking at the [CSS spec #fixed-table-layout](https://www.w3.org/TR/CSS2/tables.html#fixed-table-layout), the behavior for each `table-layout` value is:

| Mode    | How column width is determined                                                              | min/max |
|---------|---------------------------------------------------------------------------------------------|---------|
| `fixed` | Determined **only by the `width`** of `<col>`/the first row. Columns without a width **split the remaining space equally** | undefined |
| `auto`  | Based on content width (`width:100%` absorbs the slack)                                      | undefined |

Let's tackle the fixed-width problem first.

### 1. The fixed-width problem

1. Set `table-layout: fixed`
2. Give the fixed-width columns a `width`
3. Don't give the flexible column a `width` (it absorbs the remaining space)

This solves the fixed-width-column part first. The flexible column takes up all of the table width left over after the fixed columns consume theirs. So as the browser window gets wider, that column's width grows too. 

As the browser window gets narrower, that colum's width narrows too and the contents of the column will be disappear. So minimum-width is needed.

### 2. The flexible column's minimum-width problem

Since `min-width` doesn't work on a `table col`, what if we put `min-width` on the `table` itself as a workaround?

**[table's own min-width] = [sum of all fixed column widths] + [flexible column's minimum width]**

Just set it like that, right?

But per the CSS 2.1 spec we checked above, the effect of `min-width` on a table is *also* undefined. So do we give up here? Surprisingly, `min-width` works just fine on a `table`. It works in Chrome, Safari, and Firefox alike. So we can go ahead with the plan.

The code looks like this:

```tsx
const tableMinWidth = sumOfFixedColumnWidths + flexibleColumnMinWidth;

<div style={{ overflowX: 'auto' }}> // scroll appears when the container is narrower than the table's min-width
    <table style={{ 
      tableLayout: 'fixed',    // table-layout: fixed
      minWidth: tableMinWidth, // table min-width
      width: '100%',           // table fills the available container width
    }}>
      <colgroup>
        <col style={{ width: '80px' }} />     // fixed-width column
        <col />                               // flexible column
      </colgroup>
      // ...
    </table>
</div>
```

CSS really is a trash technology.

By the way, the code above breaks if there are 2 or more flexible columns.

---

## TL;DR

- The `min-width` and `max-width` styles on an HTML table column are ignored
- But the `min-width` style on an HTML table itself works
- So if you want to make a specific column flexible, work around it by putting `min-width` on the table

EOD

20260611
