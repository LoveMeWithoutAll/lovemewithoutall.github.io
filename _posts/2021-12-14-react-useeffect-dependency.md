---
layout: single
title: react useEffect는 dependency에 같은 값(?)이 들어오면 동작할까?
date: 2021-12-14 07:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react]
---

React 개발하다가 생각난 거. Object destructing으로 받아온 string값이 useEffect의 dependency로 들어있을 경우, object는 바뀌되 그 안의 string property의 값은 변경되지 않으면, useEffect는 동작할까? 검증을 위해 몇 가지 테스트를 해봤다.

useState의 state 값이 변경되면 동작하는 useEffect가 있을 때, setStateAction에 기존의 state와 똑같은 String을 넣으면 useEffect는 동작할까?

테스트해보니 useEffect는 동작하지 않는다.

테스트 한 codeSpace link: https://codesandbox.io/s/react-useeffect-test-with-same-string-state-z6bee

---
state의 type이 template literal type이라면?

https://codesandbox.io/s/react-useeffect-test-with-template-literal-state-c51qe?file=/src/TestComponent.tsx

그래도 useEffect는 동작하지 않는다.

---

하지만 state의 값이 array라면 useEffect가 동작한다.

codespace link: https://codesandbox.io/s/react-useeffect-test-with-same-array-state-yz9hf?file=/src/TestComponent.tsx

javascript에서 string은 primitive type으로서 값(value)으로 저장되고(passed by value), array는 참조(reference)값으로 저장(passed by reference)되기 때문이다.

---
object destructing으로 받아온 string 을 state 값으로 쓴다면 어떨까? 이 값을 변경한다면 useEffect는 동작할까?

https://codesandbox.io/s/react-useeffect-test-with-object-destructing-state-sdevy?file=/src/TestComponent.tsx

테스트해보니 useEffect는 동작하지 않는다. object destructing을 해서 받아온 string은 primitive value이기 때문이다. 만약 임의의 object에 속한 property를 가리키고 있는 거라면 reference value라서 useEffect가 동작하겠지만, 그런 일은 벌어지지 않았다.

---

마지막으로 하나 더. 위 예제는 object의 property가 1개였다. 만약 2개의 property가 있고, setStateAction이 useEffect의 dependency에 들어있지 않은 property를 바꾼다면? useEffect는 동작할까?

https://codesandbox.io/s/react-useeffect-test-with-object-destructing-state2-kwpqw?file=/src/TestComponent.tsx

테스트 해보니 useEffect는 동작하지 않았다.

object destructing의 경우가 궁금해서 이것저것 테스트해봤다. 끝

20211206
