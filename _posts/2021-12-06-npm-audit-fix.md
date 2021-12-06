---
layout: single
title: npm audit으로 보안취약점을 발견했을 때의 조치
date: 2021-12-06 19:01:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
    image: "https://upload.wikimedia.org/wikipedia/commons/d/db/Npm-logo.svg"
categories:
- IT
tags: [npm]
---

## 상황

1. npm audit fix로 해결하지 못하는 보안 취약점들이 존재함
2. 남은 보안 취약점들은 모두 devDependencies인 storybook, react-scripts(create-react-app)가 참조하는 node module에서 발생
3. 문제를 유발하는 storybook, react-scripts를 최신 버전으로 업데이트 해도 여전히 보안취약점 발생
	1. 해당 node module의 의존성의 의존성 중 한 곳에서 보안취약점이 존재하는 버전의 모듈을 사용하기 때문
4. npm audit fix --force 를 하면 보안취약점 숫자가 늘어남
	1. npm audit이 안전하다고 판단하는 버전으로 node module을 downgrade 하기 때문인데, downgrade된 node module의 의존성의 의존성 중 어디에선가 보안취약점이 존재하기 때문.
	2. downgrade 된 후에 다시 npm audit fix --force 를 해도 이 문제는 해결되지 않음. 위 과정이 반복될 뿐.

## 조치

1. build output에 포함되지 않는 node module은 모두 devDependencies로 옮겨라
	1. devDependencies에 의해 설치되는 node module은 보안취약점 검사를 하지 않기 위함
2. npm audit --production  으로 점검
	1. 위 명령은 devDependencies에 의해 설치된 node module은 취약점 검사를 하지 않음. 의존성 호이스팅에 의해 node_modules 디렉토리의 최상위에 설치된 node module이라 할지라도 그렇다.
		1. 의존성 호이스팅: npm은 특정 node module의 버전을 복수의 node module에서 참조할 경우, 참조되는 node module을 최상위 경로로 옮김
	2. devDependencies의 node module은 build output 에 포함되지 않기 때문에 보안취약점과 무관하다.
		1. react-scripts(create-react-app)은 bulid tool이다. build output을 만드는 역할만 한다. 배포 파일의 취약점을 만들지 않는다.
		2. storybook 역시 위와 같은 이유로 인하여 배포 파일과는 의존성이 없다.
		3. 그 외 craco 등 라이브러리도 마찬가지

## 요약

1. npm audit는 devDependencies의 취약점까지 체크하는데 이는 불필요하다.
2. 실제 빌드 산출물의 취약성을 검사하기 위해서는 devDependencies를 제외하는 옵션을 추가해서 npm audit --production 를 이용하면 된다.
	1. 이를 위해 react-script는 devDependencies로 옮겨야 한다


## 참고

- create-react-app은 보안취약점 관련 issue를 더이상 받지 않겠다는 글: https://github.com/facebook/create-react-app/issues/11174
- 유령의존성: https://rushjs.io/pages/advanced/phantom_deps/
- toss의 node module 사용법: https://toss.tech/article/node-modules-and-yarn-berry

20211028
