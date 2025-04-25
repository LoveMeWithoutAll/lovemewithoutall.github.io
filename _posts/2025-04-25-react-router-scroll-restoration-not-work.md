---
layout: single
title: React Router <ScrollRestoration /> 디버깅 기록 — 왜 스크롤이 안 움직였을까?
date: 2025-04-25 10:49:30.000000000 +09:00
type: post
header:
    teaser: "http://image.yes24.com/Goods/90265564/L"
    image: "http://image.yes24.com/Goods/90265564/L"
categories:
- IT
tags: [typescript]
---

# React Router <ScrollRestoration /> 디버깅 기록 — 왜 스크롤이 안 움직였을까?

Single-Page Application(이하 SPA)에서 뒤로·앞으로 네비게이션을 했을 때 유저가 읽던 위치로 스크롤을 복원해 주는 일은 UX 품질을 좌우합니다. React Router v6.4+는 이를 위해 <ScrollRestoration /> 컴포넌트를 제공하지만, “넣기만 하면 된다”는 공식 설명과 달리 CSS·레이아웃 제약 때문에 전혀 작동하지 않을 수 있습니다.

이 글은 <ScrollRestoration />가 꿈쩍도 하지 않았던 원인을 추적하고, Zustand + scrollIntoView 로 우회 구현하기까지의 전 과정을 기록한 디버깅 노트입니다. 특히 <html>/<body>에 지정된 height: 100vh + overflow:hidden이 어떻게 루트 스크롤 자체를 차단해 버렸는지, 그리고 실제로 overflow 설정이 “범인”임을 증명한 과정까지 상세히 다룹니다.

⸻

```
목차
	1.	문제 상황 설명
	2.	<ScrollRestoration /> 한 줄 추가 → 실패
	3.	“루트 스크롤 잠금”이 가져오는 파국
	4.	과연 overflow 가 진짜 범인인가? (실험 & 증명)
	5.	React Router v7 <ScrollRestoration /> 내부 동작 해부
	6.	해결 전략 비교 — 루트 스크롤 해제 vs 우회 구현
	7.	우회 구현: Zustand + data-id + scrollIntoView
	8.	체크리스트 & 실전 팁
	9.	회고 — 같은 삽질을 피하려면?
```
⸻

## 1. 문제 상황 설명

###		구조
	•	/qna : 무한 스크롤 Q&A 목록
	•	/answer/:id, /complaint/:id : 상세 뷰
	•	요구 : 상세 → 뒤로/앞으로 시 목록에서 이전 스크롤 위치 복원

### 글로벌 CSS

```css
html, body {
  height: 100vh;    /* (1) 문서 높이를 뷰포트와 완전히 동일하게 */
  overflow: hidden; /* (2) 루트 스크롤바 완전 제거           */
}

.c-qna {
  height: 100%;
  overflow: auto;   /* 실제 스크롤은 이 div가 담당 */
}
```

height: 100vh 는 모바일 WebView 이슈, overflow:hidden 은 패럴랙스 효과 때문에 반드시 유지해야 하는 조건이었다.

CSS 고수라면 이것만 보고도 문제의 원인을 짐작하실 수 있을 것이다.

⸻

## 2. <ScrollRestoration /> 한 줄 추가 → 실패

`<Outlet />` 옆에 한 줄만 추가하면 된다는 React router의 호언장담과 달리 스크롤은 전혀 동작하지 않았다.

```javascript
function AppLayout() {
  return (
    <>
      <ScrollRestoration storageKey="home" />
      <Outlet />
    </>
  );
}
```

	•	기대 : 뒤로/앞으로 때 자동 복원
	•	현실 : 스크롤은 늘 0 px, 경고·오류도 없음 → 디버깅 시작

### 브라우저 DevTools > Performance 패널 확인

	•	뒤로 버튼 클릭했으나
	•	정말로 스크롤 이동이 전혀 발생하지 않는다!


⸻

## 3. “루트 스크롤 잠금”이 가져오는 파국

### 관찰	결과

window.scrollTo(0, 400) 실행 했으나, 여전히 스크롤은 0px.

```javascript
const root = document.scrollingElement;  // <html>
console.log(root.scrollHeight, root.clientHeight); // 같음
window.scrollTo(0, 300);                 // 효과 없음
```

반면 내부 컨테이너의 scrollTop 은 값이 변하며 실제 스크롤바도 표시 → 실제 스크롤 주체가 div임 확인.

브라우저는 루트가 “움직일 수 없다”고 판단하므로 루트-타깃 API(window.scrollTo, ScrollRestoration)가 전부 무력화 된다.

⸻

## 4. 과연 overflow가 진짜 범인인가?

### 4-1 실험 ① — overflow:hidden 제거

html, body {
  height: 100vh;
  /* overflow: hidden; ⬅︎ 주석 */
}

	•	window.scrollTo 즉시 정상 동작
	•	<ScrollRestoration />도 바로 복원 성공

### 4-2 실험 ② — overflow 유지, height:100vh만 제거

html, body {
  /* height: 100vh; ⬅︎ 주석 처리 */
  overflow: hidden;
}

	•	여전히 스크롤 불가 (루트가 overflow hidden 이면 내부 높이와 무관하게 스크롤 차단)

### 4-3 결론
	•	overflow:hidden 이 루트 스크롤 차단의 주범
	•	height:100vh 는 scrollHeight 계산을 1 : 1 로 만드는 보조 요인
	•	두 속성이 동시에 있으면 “루트 스크롤 0px”이 확정 → ScrollRestoration 완전 무력화

[StackOverflow](https://stackoverflow.com/questions/66098013/window-scrollto-doesnt-work-with-overflow-and-100vh-height?utm_source=chatgpt.com) · [jQuery](https://github.com/jquery/jquery/issues/4581?utm_source=chatgpt.com) 이슈 등에서도 같은 현상 보고가 다수 있다. ￼ ￼

⸻

## 5. React Router v7 ScrollRestoration 내부 동작 해부
1.	route마다 Key를 지정
1.	세션 스토리지에 각 Route Key의 현재 스크롤 위치 저장
1.	POP 이동(뒤로/앞으로)할 경우, 세션 스토리지를 검사하여 스크롤 위치가 존재할 경우, window.scrollTo 함수를 호출해 스크롤 복원

#### 결국 루트 엘리먼트가 스크롤 가능한 구조가 아니면 설계상 작동이 불가능한 구조다.

⸻

## 6. 해결 전략 비교

- 루트 스크롤 구조로 전환 &	<ScrollRestoration /> 그대로: 레거시 CSS·WebView 대수정은 불가...
- React-router의 <ScrollRestoration /> 포크 → 하위 element를 스크롤 하도록 기능 확장: react router의 활발한 버전 변경 때문에 유지 보수가 어려움...
- Zustand + scrollIntoView:	레이아웃 변경 필요 없음 & 구현 단순	

가장 단순한 Zustand + scrollIntoView를 선택했다

⸻

## 7. 우회 구현: Zustand + data-id + scrollIntoView

1. Zustand 스토어를 만들어서
1. 목록 아이템을 클릭할 때마다 zustand에 아이템의 id를 저장하고
3. 목록 아이템 html element에 data-id를 부여하고
4. useEffect에서 스크롤을 복원한다

```typescript
const navType  = useNavigationType();      
const targetId = useScrollStore(s => s.targetId);

useEffect(() => {
  if (navType !== 'POP' || !targetId) return; // 뒤로가기 & 앞으로 가기 동작에서만 스크롤 복원
  requestAnimationFrame(() => {
    document
      .querySelector<HTMLElement>(`[data-id="${targetId}"]`)
      ?.scrollIntoView({ behavior: 'instant' });
  });
}, [navType, targetId]);
```

⸻

## 8. 체크리스트 & 실전 팁
	1.	CSS부터 확인 — html/body overflow, height
	2.	window.scrollTo 수동 호출 테스트
	3.	POP vs PUSH 구별(useNavigationType)
	4.	DOM 완성 타이밍 확보(requestAnimationFrame, Observer)
	5.	세션 키/스토어 키 충돌 방지

⸻

## 9. 회고 — 같은 삽질을 피하려면?
	•	Root vs Inner 스크롤 분리 여부를 최우선으로 점검하자.
	•	<ScrollRestoration /> 는 (2025-04 현재) 루트 전용 컴포넌트다. 구조가 맞지 않으면 과감히 우회하거나 다른 라이브러리를 쓰는 편이 빠르다.
	•	전역 스토어 + scrollIntoView 방식은 구현이 쉽고 레거시 레이아웃에도 잘 맞는다.

## 한 줄 요약
“window.scrollTo가 움직이지 못하면 <ScrollRestoration />도 움직일 수 없다.”
스크롤 버그를 만나면 CSS로 루트가 잠겨 있지 않은지부터 확인하자!

EOD

20250425
