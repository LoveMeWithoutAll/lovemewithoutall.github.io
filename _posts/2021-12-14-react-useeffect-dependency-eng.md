---
layout: single
title: Does react useEffect run when the dependency receives the same value(?)
date: 2021-12-14 07:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react]
---

Something that came to mind while working on React. When a string value obtained through object destructuring is in a useEffect's dependency, if the object changes but the value of the string property inside it does not change, will useEffect run? To verify this, I ran a few tests.

When there's a useEffect that runs when a useState state value changes, will useEffect run if you pass a String identical to the existing state into setStateAction?

After testing, useEffect does not run.

Link to the codeSpace I tested: https://codesandbox.io/s/react-useeffect-test-with-same-string-state-z6bee

---
What if the type of state is a template literal type?

https://codesandbox.io/s/react-useeffect-test-with-template-literal-state-c51qe?file=/src/TestComponent.tsx

Even then, useEffect does not run.

---

But if the value of state is an array, useEffect runs.

codespace link: https://codesandbox.io/s/react-useeffect-test-with-same-array-state-yz9hf?file=/src/TestComponent.tsx

This is because in javascript, a string is a primitive type that is stored as a value (passed by value), while an array is stored as a reference value (passed by reference).

---
What about using a string obtained through object destructuring as the state value? If you change this value, will useEffect run?

https://codesandbox.io/s/react-useeffect-test-with-object-destructing-state-sdevy?file=/src/TestComponent.tsx

After testing, useEffect does not run. This is because a string obtained through object destructuring is a primitive value. If it were pointing to a property belonging to some object, it would be a reference value and useEffect would run, but that did not happen.

---

One last thing. The example above had an object with one property. What if there are two properties, and setStateAction changes a property that is not in the useEffect's dependency? Will useEffect run?

https://codesandbox.io/s/react-useeffect-test-with-object-destructing-state2-kwpqw?file=/src/TestComponent.tsx

After testing, useEffect did not run.

I was curious about the object destructuring case, so I tested various things. The end.

20211206
