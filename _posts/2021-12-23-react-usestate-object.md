---
layout: single
title: React에서 object를 useState로 관리할 때 주의할 점
date: 2021-12-23 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
    image: "https://w7.pngwing.com/pngs/452/495/png-transparent-react-javascript-angularjs-ionic-github-text-logo-symmetry.png"
categories:
- IT
tags: [react]
---

React에서 object를 useState로 관리한다면, setState를 할 때 반드시 새로 object를 만들어서 넣어라. 

기존 object의 property만 변경하여 setState에 argument로 집어넣을 경우, useEffect가 변경사항을 감지하지 못한다. React는 Object의 reference address value을 기준으로 비교하여 diff를 인식하는데, 같은 object면 reference address value가 같으니, 변경된 게 없다고 인식한다. 

codeSandBox 예시
https://codesandbox.io/s/react-use-state-watch-class-l7r54?file=/src/App.tsx

20211220
