---
layout: single
title: 뒤로가기 눌렀는데 왜 여기로? Chrome과 Safari의 리디렉션 동작 파헤치기
date: 2025-07-09 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
    image: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
categories:
- IT
tags: [javascript]
---

## 뒤로가기 눌렀는데 왜 여기로? Chrome과 Safari가 다르네?

### 현상

웹 개발을 하다 보면 분명 같은 코드를 작성했음에도 브라우저마다 다르게 동작하는 황당한 경우를 마주한다. 특히 페이지 리디렉션 후 '뒤로가기' 버튼의 동작은 개발자를 혼란에 빠뜨리는 단골 소재다.

내가 겪은 현상은 이렇다.

*   **시나리오:** 사용자가 `A 페이지`에서 링크를 눌러 `B 페이지`로 이동했고, `B 페이지`는 로드되자마자 자바스크립트에 의해 자동으로 `C 페이지`로 리디렉션되었다.


```text
[ A 페이지 ]
     |
     |  사용자가 B로 가는 링크 클릭
     V
[ B 페이지 ] --- (로드되자마자 사용자 상호작용 없이 C로 자동 리디렉션) ---> [ C 페이지 ]
     |
     |  '뒤로가기' 버튼 클릭
     V
 혼돈 ????
```

이때 `C 페이지`에서 브라우저의 뒤로가기 버튼을 누르면...

**결과:**

*   **Chrome:** `B`를 건너뛰고 `A`로 돌아간다.
    *   `[ A ] <--- (B는 건너뜀) --- [ C ]`
*   **Safari:** 방문 기록에 따라 `B`로 돌아간다.
    *   `[ A ] <--- [ B ] <--- [ C ]`

이 현상은 버그가 아니다. 두 브라우저의 철학이 반영된, 명백히 **의도된 설계의 차이**다. 이 글에서는 이 차이가 왜 발생하는지, 그 근본적인 원리를 파헤치고 개발자로서 어떻게 대응해야 할지 알아본다.

[호랑이 굴(공식 문서 오피셜)에 들어온 걸 환영한다](https://html.spec.whatwg.org/multipage/browsing-the-web.html#:~:text=Welcome%20to%20the%20dragon%27s%20maw)

### 범인: Chrome의 '특별한(예외적인)' 정책

결론부터 말하면, 이 현상의 원인은 Chrome의 독자적인 사용자 보호 정책 때문이다.

*   **Safari의 동작 (`A ← B ← C`)** 은 웹 표준(Web Standard)을 그대로 따른 결과다.
*   **Chrome의 동작 (`A ← C`)** 은 표준 동작에 **'방문 기록 조작 방지(History Manipulation Intervention)'** 라는 예외적인 정책을 적용한 결과다.

의외로 Safari가 아니라 Chrome이 예외적인 동작을 했다는거다.

이제부터 각 브라우저의 입장에서 이 상황을 자세히 들여다보자.

---

### Safari는 억울하다. 표준을 따랐을 뿐

Safari의 동작은 특별한 기능이 아니라, 웹의 기본 원칙(기록과 성능)을 충실히 따른 결과다.

#### 원칙 1: 기록 - 모든 발자국은 기록되어야 한다.

웹 표준 기구인 WHATWG의 HTML Living Standard는 브라우저의 **세션 기록(Session History)**, 즉 뒤로/앞으로 가기 목록이 어떻게 동작해야 하는지 정의한다. 이 표준에 따르면, `window.location.replace()` 같은 특수한 경우를 제외하고 `window.location.href`나 링크 클릭을 통한 페이지 이동은 세션 기록 스택에 새로운 항목을 추가해야 한다.

따라서 `A → B → C` 순서로 페이지가 열렸다면, 방문 기록 스택에는 `[A, B, C]`가 순서대로 쌓이는 것이 지극히 정상이다. Safari는 이 표준을 그대로 구현했을 뿐이다.

#### 원칙 2: 성능 - 뒤로가기는 빨라야 한다.

Safari는 뒤로/앞으로 가기 성능을 극대화하기 위해 **BFCache(Back-Forward Cache)** 를 매우 적극적으로 사용한다. BFCache는 사용자가 페이지를 떠날 때, 해당 페이지의 DOM, 자바스크립트 상태 등 메모리 상태 전체를 스냅샷처럼 '얼려서' 저장해두는 캐시다.

사용자가 뒤로가기를 누르면, Safari는 신속하게 이 '얼려둔' 페이지를 즉시 되살려 보여준다. BFCache에 B 페이지를 저장해 두었기 때문이다. 개발자는 B 페이지를 보여주고 싶지 않았는데도 그렇다.

### Chrome은 왜 표준에 개입하는가? - '사용자 보호'의 원칙

반면 Chrome은 웹 표준을 따르는 것보다 사용자 경험 보호가 더 중요하다고 판단했다.

#### 배경: 악성 사이트의 '뒤로가기 함정'

과거 일부 광고 페이지나 악성 사이트는 사용자를 특정 페이지에 가두기 위해 뒤로가기 버튼을 악용했다. 사용자가 뒤로가기를 눌러도, 이전 페이지에 심어둔 리디렉션 스크립트가 다시 작동하여 원치 않는 페이지로 계속 사용자를 보내버리는 '뒤로가기 함정(Back-button Hijacking)' 문제가 있었다. 개미지옥 같은 상황이다.

#### Chrome의 해결책: 방문 기록 조작 방지 (History Manipulation Intervention)

Chrome은 이 문제를 해결하기 위해 2018년, Chrome 69 버전부터 '방문 기록 조작 방지'라는 정책을 도입했다. 이 정책의 핵심은 간단하다.

1.  페이지가 로드된 후, **사용자의 클릭과 같은 명시적인 상호작용(User Activation) 없이** 다른 페이지로 리디렉션이 발생했는지 감지한다.
2.  이런 식으로 생성된 방문 기록(우리의 예시에서는 `B 페이지`)에 내부적으로 **'건너뛰기(skip)' 플래그**를 지정한다.
3.  사용자가 뒤로가기 버튼을 누르면, 이 플래그가 지정된 방문 기록은 무시하고 그 이전 항목(`A 페이지`)으로 이동시킨다.

즉, Chrome은 "사용자가 의도하지 않은 자동 리디렉션 페이지는 유용하지 않으며, 오히려 사용자 경험을 해칠 수 있으니 뒤로가기 목록에서 제외하겠다"는 결정을 내린 것이다. Chrome에서 특정 페이지가 방문 기록에 남으려면 해당 페이지에서 반드시 사용자 상호작용이 필요한 것이다. 

JS에서는 사용자 상호작용(User Activation)을 했는지 여부를 `navigator.userActivation.isActive` 로 확인할 수 있다. 이 또한 깊고 어려운 주제다...

### 한눈에 보는 비교: Chrome vs. Safari

| 구분 | Chrome | Safari |
| :--- | :--- | :--- |
| **핵심 철학** | **사용자 보호** 우선 | **웹 표준 준수** 및 **성능** 우선 |
| **리디렉션 페이지(B) 처리** | '문제 가능성 있는' 기록으로 간주, 건너뛰기 플래그 지정 | '유효한' 방문 기록으로 간주, BFCache에 저장 |
| **뒤로가기 결과** | 중간 기록(B) **건너뛰기** | 중간 기록(B)으로 **돌아가기** |
| **관련 기술/정책** | History Manipulation Intervention | BFCache, HTML Standard |

### 그래서 개발자는 어떻게 해야 할까?

이러한 차이를 이해했다면, 이제 우리의 코드를 예측 가능하게 만들 차례다.

#### 1. 의도를 명확히 하라: `location.replace()`

만약 `B 페이지`가 인증 처리나 데이터 전송만을 위한 중간 경유지로서, 사용자가 절대로 돌아올 필요가 없는 페이지라면 `location.replace()`를 사용해야 한다.

`replace()` 메소드는 현재 페이지의 방문 기록을 새 페이지로 **대체**해버린다. 따라서 방문 기록 스택에는 `B`가 남지 않게 되므로, 모든 브라우저에서 동일하게 `C`에서 뒤로가기 시 `A`로 이동한다.

```javascript
// Before: 방문 기록에 B가 남는다.
window.location.href = '/c-page';

// After: B의 방문 기록을 C로 대체한다. (권장)
window.location.replace('/c-page');
```

#### 2. 차이를 인정하고 테스트하라

`location.href`를 사용한 리디렉션은 브라우저마다 다르게 동작하는 것이 '정상'임을 인지해야 한다. 서비스의 주요 타겟 사용자가 사용하는 브라우저 환경에서 기획된 내비게이션 플로우가 올바르게 동작하는지 반드시 테스트해야 한다.

#### 3. 사용자 경험(UX) 관점에서 결정하라

기술적인 방법을 선택하기 전에 근본적인 질문을 던져야 한다. **"사용자가 과연 이 중간 페이지로 돌아오고 싶을까?"** 만약 그럴 필요가 전혀 없다면 `location.replace()`를 사용하는 것이 정답이다. 만약 중간 페이지가 의미 있는 정보를 담고 있어 돌아올 가치가 있다면, 사용자가 혼란스럽지 않도록 명확한 UI를 제공하는 편이 낫다.

### 결론: 버그가 아니라 기능이에요

Chrome과 Safari의 뒤로가기 동작 차이는 단순한 버그나 파편화가 아니다. 이는 '사용자 보호'와 '웹 표준 준수'라는 각자의 뚜렷한 철학이 반영된 결과물이다.

이러한 브라우저의 깊은 속내를 이해하다가 복장 터져 죽겠다

## 참고
- 크롬팀의 사연(history 조작 개입): https://chromium.googlesource.com/chromium/src/+/refs/heads/lkgr/docs/history_manipulation_intervention.md
- html spec: https://html.spec.whatwg.org/multipage/browsing-the-web.html
- bfcache: https://web.dev/articles/bfcache?hl=ko

EOD

20250709
