---
layout: single
title: node_modules의 구조와 유령 의존성(phantom dependency)
date: 2021-12-03 18:01:30.000000000 +09:00
type: post
header:
    teaser: "http://image.yes24.com/momo/TopCate526/MidCate006/52551019.jpg"
    image: "http://image.yes24.com/momo/TopCate526/MidCate006/52551019.jpg"
categories:
- IT
tags: [npm, yarn, node_modules]
---

## node_modules의 구조

node_modules 디렉토리에는 package.json에 명시된 라이브러리가 저장된다. 각 라이브러리에도 독자적인 package.json이 있고, package.json에 명시된 dependencies를 각각 해당 라이브러리가 저장된 디렉토리의 하위에 node_modules 디렉토리를 또 만들어 저장한다. 이 때 복수의 라이브러리에서 동시에 같은 버전의 라이브러리를 dependency로 사용하고 있다면, 해당 라이브러리를 node_modules 디렉토리의 최상위 경로로 올린다.

![artifacts](/assets/images/2021-12-03-node-module-phantom-dependency.png)

출처: https://toss.tech/article/node-modules-and-yarn-berry

## 유령 의존성(phantom dependency)

유령 의존성(phantom dependency)는 중복 라이브러리의 위치 때문에 발생하는 현상이다. 유령 의존성은 package.json에 명시하지 않은 라이브러리를 사용할 수 있는 것이다. 이 라이브러리는 버전이 명확하지 않기 때문에, 다른 사용자에게는 버전 호환 실패로 인한 오류를 발생시킬 수 있다.


### 참고

- https://rushjs.io/pages/advanced/phantom_deps/
- https://toss.tech/article/node-modules-and-yarn-berry

20211027
