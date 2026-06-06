---
layout: single
title: What Happens in React When You Use CSS display none?
date: 2021-10-25 20:43:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react, css]
---

## A component that goes from display: block -> display: none

1. is not rendered
2. shows up in the DOM tree
3. shows up in the React DevTools Component tab
4. the component is not destroyed
	1. the component stays alive
	2. the component keeps doing its work
	3. the cleanup function returned from useEffect is not executed

-------

## So what about a component whose initial value is display: none?

Just like when it goes from display: block -> none,

1. it is not rendered
2. it shows up in the DOM tree
3. it shows up in the React DevTools Component tab
4. the component does its work
	1. useEffect runs

-------

## Conclusion

1. A React functional component returns a React element.
	1. It returns the React element regardless of whether the CSS display property is block or none
	2. All side effects are also executed at this stage
2. The browser uses the returned React element to build the DOM tree and the CSSOM tree
	1. When building the rendering tree, elements with display: none are left out
	2.  React plays no role at this stage

-------

## Example code

https://codesandbox.io/s/react-style-display-none-jf32k?file=/src/App.tsx

20210927
