---
layout: single
title: Android WebView와 document.visibilityState: 왜 ‘visible’일까?
date: 2025-02-25 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Andrdoi]
---

Android 앱 개발을 진행하면서 백그라운드에서 Chrome WebView를 실행하는 상황을 마주한 적이 있을 것입니다. 특히, 페이지 내에서 document.visibilityState를 호출했을 때 실제 화면에 웹뷰가 보이지 않음에도 불구하고 "visible"이라는 결과가 반환된다면 당황스러울 수 있습니다. 이번 포스트에서는 이 현상이 발생하는 이유와 그에 따른 고려사항, 그리고 개발자가 취할 수 있는 조치에 대해 자세히 살펴보겠습니다.

## 문제 상황 소개

Android 앱 내에서 WebView를 활용하여 콘텐츠를 로드할 때, 다음과 같은 상황이 발생합니다.

	* 백그라운드 실행: 사용자가 현재 보지 않는 화면에 WebView가 존재하거나, 앱의 다른 부분에 의해 가려진 상태에서도 WebView는 계속 동작하고 있습니다.
	* Visibility API 결과: 페이지 내 자바스크립트 코드에서 document.visibilityState를 호출하면, 실제로는 보이지 않음에도 불구하고 "visible"이라는 값이 반환됩니다.

이러한 현상은 특히 리소스 최적화나 백그라운드 작업 관리 측면에서 혼란을 줄 수 있습니다.

## Android WebView와 Page Visibility API

### 1. WebView의 뷰 계층 구조

Android 앱은 여러 View로 구성된 계층 구조를 갖고 있습니다. WebView가 화면에 실제로 보이지 않더라도,
앱의 View 계층 내에 남아 있다면 브라우저 엔진은 해당 WebView를 “활성”으로 간주합니다.
즉, 실제 렌더링 여부와 관계없이 WebView가 메모리상에 존재하면, 그 상태는 여전히 활성으로 판단됩니다.

### 2. Page Visibility API의 설계 목적

document.visibilityState와 같은 Page Visibility API는 주로 브라우저 탭의 활성화 여부를 감지하기 위해 설계되었습니다.
일반적인 웹 브라우저 환경에서는 사용자가 현재 보고 있는 탭과 그렇지 않은 탭을 구분할 수 있지만,
Android WebView는 애플리케이션 내의 하나의 뷰로 취급되어 별도의 visibility 상태 변경 이벤트가 발생하지 않습니다.

	핵심 포인트: Android WebView가 앱의 View 계층에 남아 있다면, 백그라운드에 있더라도 “visible” 상태로 간주됩니다.

## 백그라운드 동작 제어를 위한 고려사항

만약 백그라운드 상태에서 불필요한 리소스 소모를 방지하거나, 의도한 대로 상태 관리를 하고자 한다면 다음과 같은 방법을 고려할 수 있습니다.

### 1. 명시적 상태 제어
#### onPause()와 onResume() 메서드 활용
WebView의 생명주기 메서드를 적절히 호출하여, 백그라운드에서의 불필요한 활동을 중단할 수 있습니다.
예를 들어, 앱이 백그라운드로 전환될 때 webView.onPause()를 호출하고, 다시 포그라운드로 복귀할 때 webView.onResume()를 호출하면
WebView 내부의 활동을 제어할 수 있습니다.

### 2. 추가적인 사용자 정의 로직 구현
#### 자체적인 visibility 체크:
앱 내에서 WebView의 실제 표시 여부를 판단하는 별도의 로직을 추가할 수 있습니다. 이를 통해, WebView가 사용자에게 보이지 않는 상황임을 감지하고, 백그라운드 작업을 조절할 수 있습니다.
브라우저의 `sessionStorage` API를 활용하는 방법을 추천합니다.

## 결론

Android WebView가 백그라운드에서 실행 중일 때 document.visibilityState가 "visible"로 반환되는 이유는, WebView가 앱의 View 계층에 남아 있고 브라우저 엔진이 이를 활성 상태로 인식하기 때문입니다.
Page Visibility API는 주로 브라우저 탭을 대상으로 설계되었기 때문에, Android WebView의 경우 별도의 visibility 변화 이벤트가 발생하지 않습니다.

따라서, 앱 내에서 리소스 최적화나 WebView 상태 제어가 필요한 경우,
onPause()/onResume() 등의 메서드를 활용하거나 추가적인 로직을 구현하여 백그라운드 동작을 직접 관리하는 것이 필요합니다.

이와 같은 원리를 이해하고 적절한 조치를 취한다면, 보다 효율적인 앱 개발과 최적화가 가능할 것입니다.
개발에 도움이 되길 바라며, 추가적인 질문이나 공유하고 싶은 경험이 있다면 댓글로 남겨주세요!

라고 chatGPT가 써줬다. 나보다 글 잘 쓰는 듯.

20250225
