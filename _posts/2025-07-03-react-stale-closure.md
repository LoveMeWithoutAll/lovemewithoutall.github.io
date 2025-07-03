---
layout: single
title: Next.js가 closure를 만들어서 컴포넌트 초기값으로 상태를 저장해버린 건에 대하여(stale closure)
date: 2025-07-03 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
    image: "https://w7.pngwing.com/pngs/643/143/png-transparent-nextjs-hd-logo.png"
categories:
- IT
tags: [Next.js, React]
---

# 환경

- Next.js 14

# 현상

React/Next.js 기반 페이지의 Footer 컴포넌트에서 Next.js의 `Script` 컴포넌트를 사용하여 외부 Script를 로드한 뒤, `Script` 컴포넌트의 `onReady` 콜백 안에서 페이지 navigate 될 때마다 `sendLog` 함수를 호출하도록 구현했다. 일반적인 페이지 흐름에서는 `sendLog` 함수가 컴포넌트 내부의 `src` 등 상태를 정상적으로 전송했다. 그러나 문제는 Script 컴포넌트의 `onReady` 콜백(예시: `onReady(() => sendLog())`) 안에서 호출될 때였다. 이 경우 `sendLog`는 초기 렌더 시점의 상태(즉 fallback 값)만 전송했다. 디버깅 결과, `onReady` 콜백은 Script가 최초 로드될 때 한 번 등록된 함수가 실행되는데, 이때 함수가 **초기 상태를 클로저로 캡처**한 채로 실행되고 있었다.

즉, React 함수형 컴포넌트의 렌더 과정에서 정의된 `sendLog` 함수가 처음 마운트될 당시의 상태 값을 클로저(closure)으로 저장했다. 보다 상세히 설명하면 다음과 같다. 이후 상태가 업데이트 되어 컴포넌트가 리렌더링 되었음에도 불구하고, Script의 `onReady` 이벤트가 트리거되며 실행된 `sendLog`는 **초기 렌더 시점의 값만 참조**한 채 동작한 것이다. 이는 자바스크립트의 클로저(closure) 메커니즘 때문이었다. **클로저**는 “함수와 그 함수가 정의될 당시의 렉시컬 환경(스코프)을 함께 기억한 형태”를 말한다. 쉽게 말해, `sendLog`가 정의되던 당시의 상태가 함수의 내부 메모리에 저장되었고, 후에 함수가 호출될 때 이 저장된 값만 보게 된 것이다.

이번 문제의 핵심은 React의 함수형 렌더링과 JS 클로저의 결합에서 발생하는 **“Stale Closure”** 문제였다. 함수형 컴포넌트에서 렌더마다 새로운 함수와 환경이 만들어지고, 그 함수들이 자바스크립트의 클로저 규칙에 따라 값을 캡처한다. 이로 인해 나중에 실행되는 콜백 함수가 상태 변경으로 인한 리렌더링 이전에 만들어진 함수를 참조하고 있을 때, 함수가 최신 상태를 반영하지 못하고 과거의 상태 값을 사용하게 되는 것이다. 

이제 디버깅 과정을 따라가며 React hook과 클로저의 동작 원리를 검토하고, 최종적으로 `useRef` 기반 패턴으로 문제를 해결해보자.

```tsx
// 오류가 발생하던 Footer 컴포넌트
'use client';

import Script from 'next/script';
// ...

// 하단 툴바 컴포넌트
const Footer = () => {
  const [src, setSrc] = useState('');

  const sendLog = () => {
    // src를 외부 서버에 전송
    console.log('sendLog', src);
    // ...
  };

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderTabbar({ onBeforeNavigate: sendLog })}
    ></Script>
  );
};

export default Footer;

```

## React 함수형 컴포넌트와 클로저

React 함수형 컴포넌트는 호출될 때마다 렌더링 로직 전체가 재실행된다. 이 과정에서 컴포넌트 안에 선언된 모든 함수와 변수는 각각 해당 렌더의 **렉시컬 컨텍스트(실행 컨텍스트)** 에 묶인다. 따라서 함수형 컴포넌트 내에서 정의한 함수도 그 렌더링 시점의 `props`와 `state` 값을 함께 캡처한 클로저로 동작한다. Dan Abramov는 이를 “함수 컴포넌트는 렌더링된 값을 *캡처*한다”라고 설명했다. 예를 들어, 다음과 같은 예제에서:

```jsx
// react
function MessageThread() {
  const [message, setMessage] = useState('');
  const showMessage = () => {
    alert('You said: ' + message);
  };
  // ...
}
```

`showMessage`는 렌더링 당시의 `message` 값을 클로저로 닫는다. 즉, 컴포넌트가 렌더링될 때마다 새로운 `showMessage` 함수가 만들어지고, 이전 렌더의 `showMessage`는 그때의 `message` 값을 기억한다. (이는 클래스 컴포넌트에서 `this.state`를 항상 최신으로 읽는 것과 다른 차이다... 고 Dan이 그러는데, 나는 클래스 컴포넌트를 안 써봐서 모르겠다) 실제 예제에서 메세지 입력란의 값을 바꾸고 버튼을 눌렀을 때, 클릭 핸들러 내의 `message`는 “버튼을 눌렀던 그 렌더”의 값을 사용한다. Dan Abramov은 “이 함수 컴포넌트의 `message`는, 브라우저에서 호출된 클릭 핸들러를 반환한 바로 그 렌더의 `message`를 갖는다”고 설명했다.

이처럼 함수형 컴포넌트의 함수들은 자신의 **정의 시점에 있던 상태 값들**을 참조한다. 이후 상태가 업데이트되어 컴포넌트가 재렌더링되더라도 이전 렌더에서 생성된 함수들은 여전히 자신이 캡처한(닫은) 옛값을 참조한다. 그리고 만약 외부 이벤트나 비동기 콜백으로 이전 렌더 시점의 함수를 호출하게 되면, 이 함수는 당시에 캡처된 값만을 보게 된다. 이러한 메커니즘이 \*\*클로저(closure)\*\*의 핵심이다. JS만을 사용할 때는 너무나 당연했던 이 현상이 React 함수형 컴포넌트를 개발할 때는 염두에 두기 쉽지 않다. 함수형 컴포넌트가 상태 값이 변경될 때마다 컴포넌트 리렌더링으로 함수도 새로 만들어지다보니, 컴포넌트 내부의 함수가 클로저로서 동작하여 예전 상태를 참조하는 현상을 겪는 일이 드물기 때문이다. 함수형 컴포넌트 자체가 하나의 거대한 클로저임에도 불구하고 말이다.

다시 디버깅으로 돌아가자. `sendLog` 역시 컴포넌트의 처음 렌더링 시점에 만들어진 함수였기 때문에 당시 `src` 등의 상태 값을 클로저로 갖고 있었다. 다음 렌더링 시점에 상태가 변경되어도 `sendLog`의 내부에 저장된 클로저 환경은 바뀌지 않는다. 때문에 Script의 `onReady` 콜백 안에서 `sendLog()`를 등록하면, 그 함수는 최초 렌더 때 캡처된 (더 이상 갱신되지 않은) 상태 값을 기반으로 로그를 전송하게 된 것이다.

# 디버깅 

## 클로저 확인하기

문제를 추적하는 과정은 보통 **로그 찍기**와 **실행 흐름 분석**으로 시작한다. 먼저 `sendLog` 함수 시작점에 콘솔 로그를 찍어보니, onReady 실행 시 `src` 값이 초기값(fallback)으로만 나온다. 반면 평상시 이 함수를 호출하면 `src`에 최신 동영상 소스 URL이 찍혔다. 즉, 동일한 함수임에도 불구하고 onReady 쪽에서는 상태 업데이트 결과가 반영되지 않았다.

다음으로 onReady 콜백이 언제 실행되는지를 확인했다. Next.js의 `next/script` 컴포넌트에서 `onReady` 속성은 **스크립트가 로드된 직후, 그리고 컴포넌트가 마운트될 때마다 실행**된다. 우리 경우 Footer 컴포넌트가 처음 마운트되면 스크립트 로드가 완료된 시점에 `onReady` 콜백이 실행된다. 이때 onBeforeNavigate 프로퍼티에 `sendLog`를 등록한다. 문제는 이후 페이지 내에서 video 관련 상태(예를 들어 src가 변경)가 업데이트된 시점에도, `onReady` 콜백은 초기 마운트에서 만든 동일한 함수를 그대로 사용한다는 점이다. 즉, `sendLog` 함수를 정의했던 당시의 상태가 고정된 채로 남아 있고, 이후 렌더에서 바뀐 값들은 호출되는 클로저 내부의 상태 값에 반영되지 않는다.

이러한 동작을 이해하려면 React의 렌더 주기를 파악해야 한다. React는 상태가 변경될 때마다 해당 컴포넌트를 다시 실행하고 새로운 함수/변수들을 생성한다. 그러나 이미 DOM에 붙은 이벤트 핸들러나 콜백은 이전 렌더에서 만들어진 함수를 그대로 참조한다. 위 예제에서도 마운트 시점에 `sendLog`를 참조하는 onReady 콜백이 등록되었기 때문에, 이후의 상태 변화와 관계없이 그 콜백은 변하지 않고 여전히 **초기 상태를 캡처한 클로저**인 것이다.

## 자바스크립트 렉시컬 스코프와 클로저

앞선 내용을 좀 더 일반적인 자바스크립트 개념으로 풀어보자. JS에서 **클로저**는 함수와 그 함수가 정의된 환경(렉시컬 환경)을 묶은 것이다. 예를 들어:

```js
function outer() {
  let count = 0;
  function inner() {
    console.log(count);
  }
  count++;
  return inner;
}
const fn = outer();
fn(); // 1
```

위 코드는 `inner` 함수가 `outer`의 `count` 변수를 닫음(closing)으로써, `inner`가 호출될 때 `count` 값을 기억하고 출력한다. React 컴포넌트 안에서도 마찬가지다. 함수 컴포넌트 내부에 정의된 함수는 그 렌더의 지역 변수(상태, props 등)에 접근할 수 있고, 렌더가 끝나도 해당 값을 기억한다. 즉, 첫 렌더에서 `src = ""`이었다면, 그 렌더에서 생성된 함수는 `src`가 ""인 상태를 기억한다. 상태가 업데이트되어도 과거 클로저는 여전히 업데이트 전 값을 유지한다.

따라서 디버깅 과정에서 “왜 `sendLog`가 항상 동일한(과거) 값을 보내는가”에 대한 답은 **클로저의 렉시컬 캡처(lexical capture)** 에 있었다. 함수가 정의된 순간의 스코프 변수들이 함수 내부에 보존되며, 이후에 참조할 수 있는 구조다. 클로저는 편리하지만, React와 만나면 상태의 “생애 주기(lifecycle)에 따른 유효 기간 만료(stale)”을 고려해야 하는 복잡함이 생긴다. 특히 비동기나 이벤트와 결합될 때에는 **의도한 시점의 최신 값**이 아닌 **함수가 처음 캡처한 값**을 보게 되는 경우가 생긴다.

## React 상태-컴포넌트 생애주기 관계

React 함수형 컴포넌트는 상태(state)와 생애주기(lifecycle)를 훅(hooks)으로 관리한다. `useState`로 선언된 상태는 렌더 시점에 따라 값이 결정된다. 예를 들어 `const [src, setSrc] = useState("")`이면, 첫 렌더 때 `src`는 ""이고, 이후 상태가 변경되어 다시 렌더되면 그 시점의 `src` 값이 함수 안에서 보인다. 그런데 **클로저는 이러한 상태 변화 결과를 자동으로 반영하지 않는다**.

Dan Abramov가 지적했듯이, 함수형 컴포넌트에서는 함수 자체가 언제나 “그 함수가 생성된 바로 그 렌더”의 상태와 props를 바라본다. 예를 들어 메시지 예제에서 “Send” 버튼을 눌렀을 때 `showMessage`는 그 순간의 `message`를 보여준다. 이후 입력된 내용은 이 기존 함수로는 볼 수 없다. 이것이 “stale closure”가 발생하는 전형적인 패턴이다.

클로저를 이해하기 위해 컴포넌트 생애주기를 보면, 컴포넌트는 마운트(Mount) 시 초기 렌더가 일어나고, 상태 업데이트시마다 재렌더링된다. 만약 다음과 같이 비동기 로직이 걸려 있다면:

```jsx
function Footer() {
  const [src, setSrc] = useState('');

  const sendLog = () => {
    // src를 외부 서버에 전송
    console.log('sendLog', lapCount, src);
    // ...
  };

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderTabbar({ onBeforeNavigate: sendLog })} // 상태가 왜 초기 값으로 나오는지 의심됨
    />
  );
}
```

여기서 `sendLog`는 최초 렌더 때의 `src=''`를 캡처한다. 스크립트가 로드될 때 onBeforeNavigate 프로퍼티에는 `sendLog()`가 등록된다. 하지만 이 함수 내부에서 보는 `src`는 **처음 렌더 당시**의 값이다. 이후 `setSrc`로 상태를 변경해도, 이미 등록된 onReady 콜백 안의 `sendLog`에는 그 변화가 반영되지 않는다.

# 해결

## `useRef`를 통한 최신 상태 확보

이 문제를 해결하려면, 언제든지 최신 상태 값을 읽을 수 있는 방법이 필요하다. 또한 리렌더링을 유발할 필요가 없을 뿐만 아니라 리렌더링을 하더라도 값이 계속 유지되는 방법이어야 한다. 이럴 때는 `useRef`를 사용하면 된다. useRef가 리턴하는 `ref` 값은 “단 하나만 존재하는 객체(=참조형 값)” 이고, 클로저(또는 외부 라이브러리)가 기억하는 것은 그 객체의 주소이다. 객체 참조는 불변값과 다르게 “얕게” 복사되기 때문에 ref의 프로퍼티인 `current`(ref.current)에 최신 값을 계속 갈아끼우면 된다. **함수는 클로저 내에서 최초 렌더링 시점의 ref을 계속 참조하겠지만, 함수가 참조하는 ref.current는 필요한 시점에 저장/읽을 수 있다.** 이로써 sendLog 함수는 의도한 대로 최신 값을 사용한다.

이것이 stale closure 회피 패턴이다.

예를 들어 위 `Footer` 예제에 `srcRef`를 추가한다면:

```jsx
function Footer() {
  const [src, setSrc] = useState('');
  const srcRef = useRef(src);

  // 상태 변경 시 ref 업데이트
  useEffect(() => {
    srcRef.current = src;
  }, [src]);

  const sendLog = () => {
    // ref.current 사용: 항상 최신 값을 읽음
    console.log('sendLog (ref)', srcRef.current);
    // ...
    // 실제 전송 로직도 srcRef.current 이용
  };

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderTabbar({ onBeforeNavigate: sendLog })}
    />
  );
}
```

이렇게 하면, **클로저 내부라도** `src.current`는 항상 가장 최신의 `src`를 가리키게 된다. ref에 값만 저장하기 때문에 함수가 원래 캡처한 상태 환경을 “벗어나” 현재 값을 참조할 수 있다. 어느 시점의 렌더에서 `sendLog`가 실행되든지 간에, `sendLog`가 참조하는 것은 `srcRef.current`이므로, 업데이트된 상태를 그대로 읽을 수 있다.

이 패턴에서 중요한 점은 **ref가 컴포넌트 전 생애주기 동안 동일한 객체를 유지**한다는 것이다. 위 예제처럼 `useEffect`로 상태 변화를 감지해 ref를 갱신해 두면, 이후 언제나 `ref.current`가 “가장 최근 값”으로 남아있다. Dmitri Pavlutin도 “ref는 실제(ref.current)로 최신 값을 항상 평가하므로, `stale closure` 문제를 피할 수 있다”라고 강조했다. 덕분에 외부 콜백에서 참조하더라도 값이 낡지 않는다(non-stale & freshest).

**stale closure 회피 패턴**을 응용하면 **함수 자체를 ref에 넣어서 호출할 수도 있다.** sendLog 함수를 `useEffect를` 사용하여 리렌더링 될 때마다 ref에 넣으면, ref.current가 참조하는 sendLog 함수는 항상 최신 렌더링에서의 함수가 된다. 따라서 클로저로서 가지고 있는 상태 값들은 항상 최신 값이다. 함수 하나만 ref에 넣고 계속 갱신하면 되니, 함수가 사용하는 모든 값들을 ref에 넣어야 하는 위 방법보다 **편리하다.**

```jsx
function Footer() {
  const [src, setSrc] = useState('');

  const sendLog = () => {
    // ...
    // 함수 내부에서 src 사용
  }

  const sendLogRef = useRef(sendLog);

  // 리렌더링 될 때마다 최신의 함수를 항상 ref에 넣는다
  useEffect(() => {
    sendLogRef.current = sendLog;
  }, [sendLog]);

  return (
    <Script
      src="/footer-sdk.js"
      onReady={() => renderFooter({ onBeforeNavigate: sendLogRef.current })} // onBeforeNavigate 프로퍼티는 최신의 sendLog 함수를 참조한다
    />
  );
}
```

## 코드 흐름 복기

위 `useRef` 패턴이 어떻게 동작하는지 정리해보자. 페이지가 로드되어 Footer 컴포넌트가 마운트되면 초기 상태는 `{ src="" }`이다. 이때 `srcRef.current=""`로 초기화된다. Script가 로드되고 `onReady`가 트리거되면, `renderFooter` 함수에서 `sendLog`가 호출된다. 이때 실행되는 `sendLog` 함수는 최초 렌더의 함수 객체이지만, 내부에서 읽는 값은 `src.current`이다. 현재 컴포넌트가 재렌더된 상태라면 `src.current`는 최신값(예: "https://11st.co.kr")을 가리키고 있다. 따라서 로깅/전송 시 최신 정보를 담는다.

반면 이전 코드에서는 `sendLog`가 처음 생성됐을 때 `src=""`이 내부 메모리에 고정돼 있었다. 그래서 이후 상태가 "https://11st.co.kr"가 되도 그 함수는 여전히 ""만 사용했다. **stale closure 회피 패턴**을 사용하면, “함수 생성 시점과 관계없이” 참조 대상(ref)이 업데이트되므로 실제 필요한 값을 얻을 수 있다. 마찬가지로 `sendLog` 함수 자체를 ref에 넣어 **stale closure 회피 패턴**을 사용한다면 같은 효과를 볼 수 있다. Dan의 Overreacted 블로그에서도 “ref에 저장한 값을 읽으면 최신 값”을 예시 코드로 보여주며, input 변화 시에도 `ref.current`는 항상 업데이트된 값을 반영한다고 설명하고 있다.

## 클로저 방어적 코드 작성 전략

마지막으로 클로저로 인한 버그를 예방하기 위한 전략들을 정리하자.

* **useRef 활용:** 위에서 살펴본 대로, 외부 이벤트나 비동기 로직이 최신 값을 참조해야 하는 경우 `useRef`를 사용해 항상 최신 상태를 저장해 두는 방식이 안전하다. 특히 렌더 간에 값이 변해도 변화가 실시간으로 반영되어야 할 때 유용하다.
* **의존성 배열 관리:** `useEffect`나 `useCallback`에서는 상태가 변경될 때마다 해당 훅이 재실행되도록 정확한 의존성을 지정해야 한다. 그래야 변경된 상태가 새 클로저에 반영된다. Lint 도구의 `react-hooks`을 활성화 해서 누락된 의존성을 잡아주자.
* **함수형 상태 업데이트:** 비동기 콜백이나 타이머 등에서 상태를 변경할 때는 함수형 업데이트(`setState(prev => new)`)를 사용하면 현재 상태 기준으로 계산해 준다(setState에서 `prev`의 존재는 축복이다). Pavlutin도 “콜백에 `setCount(c => c+1)` 형태를 쓰면 stale closure 문제가 해결된다”고 설명했다.
* **구조 변경:** 때로는 복잡한 비동기 흐름 대신 `useEffect` 내 로직 재배치나, 외부 라이브러리 호출 위치 변경 등을 통해 클로저에 묶이지 않도록 구조를 바꾸는 것도 방법이 될 수 있다. 물론 이런 복잡한 상황 자체를 만들지 않는 구조로 개발하는 것이 최선이다.

이러한 전략을 조합하면, 예상치 못한 시점에 발생하는 stale closure 버그를 크게 줄일 수 있다. 결론적으로 React 함수형 컴포넌트에서는 **클로저가 capture한 값이 언제, 어디서 사용되는지**를 항상 고려해야 한다. 클로저가 캡처된 과거 값이 아니라 **최신 데이터**가 필요할 땐, `useRef`나 함수형 업데이트 같은 패턴을 통해 명시적으로 최신 값을 유지하도록 해야 한다.

**참고자료:** 
- [How Are Function Components Different from Classes? [번역]](https://ideveloper2.dev/blog/2019-03-12--how-are-function-components-different-from-classes/)
- [Be Aware of Stale Closures when Using React Hooks](https://dmitripavlutin.com/react-hooks-stale-closures/#:~:text=component,actual%20value%20of%20the%20ref)
- Next.js `<Script>` 컴포넌트 이벤트 설명: [Next.js 공식 문서](https://nextjs.org/docs/pages/guides/scripts#:~:text=the%20script%20will-,only%20load%20once,-%2C%20even%20if%20a)

EOD

20250703
