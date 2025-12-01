---
layout: single
title: 웹브라우저 영상 자동 재생 정책(Autoplay policy)의 대혼란
date: 2025-12-01 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://webkit.org/wp-content/uploads/forever.gif"
    image: "https://webkit.org/wp-content/uploads/forever.gif"
categories:
- IT
tags: [video, safari]
---

## 웹브라우저 Autoplay policy

웹브라우저의 영상(video) 재생 정책은 사용자 경험 관점에서 대단히 중요하다. 그리고 여러 정책 중 가장 중요한 것은 Autoplay policy(자동 재생 정책)이다. 이 정책을 통하여 브라우저는 사용자 혹은 개발자에게 둘 중 하나를 택하도록 강제한다. 음소거 상태로 자동 재생되거나, 재생되지 않거나, 둘 중 하나다. 영상 관련 서비스를 개발하는 프론트엔드 개발자라면 대체로 영상 자동 재생을 구현하는 방법을 찾아 헤맨다.

그렇다면 영상은 어떤 조건이 충족되었을 때 자동 재생 되는가? 대체로 다음과 같다.

1. 명시적인 사용자 제스처로 play 버튼을 눌렀을 때
2. video element에 audio track이 없거나 muted property가 설정되어 있을 때

위 정책은 명쾌해 보이지만 실은 그렇지 않다. 심연을 파고들어 보자.

## Autoplay는 명시적 표준이 아닌 브라우저 정책 영역

HTML 명세에는 `<video autoplay>` 속성이 존재한다. 하지만 자동재생이 실행되는 조건(특히 소리가 있는 경우)은 표준으로 엄격히 정해진 것이 아니다. 각 브라우저의 정책에 따라 달라진다. 

iOS Safari는 과거부터 배터리와 사용자 경험을 위해 자동재생을 엄격히 제한해 왔다. 예를 들어 iOS 10에서는 `<video>` 엘리먼트에 오디오 트랙이 없거나 muted 속성이 있는 경우에만 사용자 제스처 없이 autoplay를 허용했고, 음소거 상태였던 영상이 동적으로 소리가 생기거나 unmute되면 즉시 일시정지하도록 정책을 정했다. 영상 2개를 동시에 재생할 수도 없었고, 1번의 사용자 제스처로 여러 영상에 unmuted autoplay 권한을 주지도 않았다. 또한 자동재생은 화면에 보이는 경우에만 시작되고, 화면을 벗어나면 중지되는 등의 규칙도 있었다. 이러한 autoplay 제한은 브라우저별 구현 사항이기에, 개발자는 브라우저 문서를 찾아볼 수밖에 없다.

Apple 공식 블로그에서도 "여러 영상을 잇따라 재생하려면 `<video>` 요소를 여러 개 만들지 말고, 하나의 `<video>` 요소 소스만 교체하는 식으로 처리하라"는 권고까지 있었다. 아래 링크는 몇 안 되는 영상 정책 관련 공식 문서다.

- [New `<video>` Policies for iOS](https://webkit.org/blog/6784/new-video-policies-for-ios)
- [Auto-Play Policy Changes for macOS](https://webkit.org/blog/7734/auto-play-policy-changes-for-macos)

읽어보면 알겠지만 2016~2017년에 작성된 위 문서의 설명은 충분하지 않을 뿐더러, 2025년 현재로서는 위 문서에서 명시 혹은 암시된 대로 동작하지도 않는다. 이후에 나온 Apple의 웹 개발 가이드나 WebKit 블로그를 통해 일부 정책이 알려져 있지만, 세부적인 변경(예: iOS 17에서의 동작 변화)은 공식 릴리즈 노트에 구체적으로 언급되지 않는다. 

그 이유에 대해서는 대략 짐작 가능하다. 애초에 autoplay policy는 HTML spec으로 정해진 것이 아닐 뿐더러, 90년대 후반 ~ 2000년대 초반의 인터넷을 경험한 사람들에게 영상 자동 재생은 대개 사용자를 불쾌하게 만들 뿐이다. 모바일 기기에서라면 기기 성능 문제까지 있다. 영상 다운로드-디코딩-디스플레이 출력과 동시에 사용자 제스처에 기민하게 반응하는 것은 모바일 환경에서는 오랫동안 불가능한 일이었다.

 결국 개발자들은 직접 실험하거나 WebKit 소스 코드를 분석해서 이러한 변화를 알아내야 한다. 다시 말해, 자동재생은 표준보다 브라우저 정책 영역에 가깝기 때문에, 최신 iOS에서 어떤 동작이 가능한지는 코드를 들여다보거나 실제 기기에서 테스트해 확인하는 수밖에 없다.

## iOS 17의 변화: 한 번의 제스처로 다수 영상의 음소거 해제 자동재생 권한 부여

**iOS 17부터 safari 엔진(WebKit)**에는 눈에 띄는 정책 완화가 발견되었다. 단 하나의 사용자 제스처로 현재 문서에 존재하는 모든 `<video>` 엘리먼트를 소리 켠 상태로 자동 재생 할 수 있는 권한을 획득할 수 있게 되었다.

예를 들어, 사용자가 화면을 터치하는 한 번의 이벤트 핸들러 안에서 비디오의 play()를 호출하면, iOS 17 safari 및 WKWebView 환경에서는 이벤트 핸들러 호출 시점에 생성되어 있던 모든 video element가 unmute 상태에서 자동 재생 권한을 획득한다. 

이전 iOS (예: iOS 16.4 이하)에서는 동일한 상황에서 첫 번째 영상만 재생되고 나머지는 차단되거나 음소거 처리되곤 했지만, iOS 17에서는 이러한 제약이 풀린 것을 확인했다. 이 사실은 iOS 17을 실행하는 아이폰 실제 기기에서 모바일 Safari와 WKWebView로 각각 실험하여 검증했다. 특히 WKWebView의 기본 구성(별도 설정 없이 기본 autoplay 정책 사용) 하에서도 동일한 결과를 얻었다.

이 변화는 공식 문서에는 명시되지 않았지만, 개발자 입장에서는 큰 의미가 있다. 이제 틱톡형 피드 구현 시 첫 동영상에서 사용자가 한 번 재생을 허용하면, 그 이후 이어지는 동영상을 연속으로 소리와 함께 자동재생시키는 것이 가능해졌다. 이는 곧 iOS 17 이상의 Safari 환경에서 사용자 경험을 한층 TikTok에 가깝게 구현할 수 있음을 의미한다. 물론 여전히 사용자가 명시적으로 해당 사이트의 자동재생을 허용/차단하는 설정이나, 저전력 모드 등의 변수는 존재한다. 그러나 기본 정책 자체는 크게 완화된 것이다.

## User Activation

그렇다면 사용자 제스처란 구체적으로 무엇을 가리키는가? 이 문제를 이해하려면 User Activation (사용자 활성화) API와 "사용자 제스처"의 범위를 알아둘 필요가 있다. HTML 스펙에서는 특정 웹 API를 남용하여 사용자에게 방해되는 행동(예: 팝업 열기, 알림 소리내기 등)을 막기 위해 사용자 활성화 개념을 도입했다. 한마디로 사용자가 의미있는 상호작용(클릭, 키다운 등)을 했을 때에만 허용되는 API들이 있는 것이다. 미디어 재생도 그 중 하나다.

User Activation API는 `navigator.userActivation`로 확인할 수 있다. iOS에서는 16.4 버전부터 가능하다.

```javascript
> navigator.userActivation

UserActivation {hasBeenActive: true, isActive: true}
```

`UserActivation` 객체의 두 프로퍼티가 가리키는 의미는 다음과 같다.

- isActive -> 임시 활성화
- hasBeenActive -> 고정 활성화

사용자 활성화의 범위(scope)는 2종류로 나뉜다. **임시 활성화(transient activation)** 와 **고정 활성화(sticky activation)** 이다. MDN 문서를 보면 이해하기 쉽다.

- [임시 활성화](https://developer.mozilla.org/en-US/docs/Glossary/Transient_activation)
- [고정 활성화](https://developer.mozilla.org/en-US/docs/Glossary/Sticky_activation)


간단히 설명해 보자. transient activation으로 얻은 권한은 일시적으로만 활성화 된다. sticky activation으로 얻은 권한은 이후에도 계속 유지된다. 두 권한은 브라우저 탭(window) 전체에 적용되고, 같은 페이지 내 동일 출처일 경우 적용된다.

[HTML Media element의 Autoplay는 일단 sticky activation이 적용된다](https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/User_activation#comparison_between_transient_and_sticky_activation:~:text=Autoplay%20of%20Media%20and%20Web%20Audio%20APIs%20(in%20particular%20for%20AudioContexts)). 하지만 safari는 MDN 문서대로 동작하지 않는다. 많은 함정이 있다.


### 함정1. "사용자 제스처로 인해 실행되었다"는 조건을 만족해야 한다

예를 들어 이런 코드는 당연히 사용자 gesture 결과로 video.play()가 호출되니까 허용된다.

```javascript
button.addEventListener('click', () => { video.play(); }) 
```

반면에 처럼 사용자 이벤트와 직접 상관없는 콜백에서 재생을 시작하면 제스처로 인한 호출로 인정되지 않는다.

```javascript
video.addEventListener('canplaythrough', () => { video.play(); })
```

핵심은 브라우저 call stack 상에서 이벤트 핸들러 -> play() 순서로 바로 이어져야 한다는 것이다.

### 함정2. React에서 "transient activation"만 활성화 시켰을 경우

webkit과 html spec을 논하다가 갑자기 React가 왜 나오는가 의아할 것이다. 하지만 React fiber와 비슷한 아키텍쳐를 가진 UI 렌더링 라이브러리는 앞으로도 계속 나올테니 반드시 짚고 넘어가야 한다. transient activation은 1번의 사용자 제스처로 인하여 브라우저 콜스택에 쌓인 명령들이 실행되는 동안에만 활성화 된다. 문제는 React fiber가 무거운 작업을 일시중지 시킬 때 발생한다. 

React의 동작 모델, 특히 Fiber 스케줄러는 렌더링 작업을 여러 조각으로 쪼개고 필요에 따라 일시중지(yield)하거나 나중에 재개하는 특징이 있다. 사용자가 클릭하고 그 콜백에서 setState를 호출하면, React는 렌더와 DOM 업데이트를 비동기적으로 스케줄링할 수 있고, 그 사이에 브라우저의 transient activation이 만료되어 버린다. 결과적으로 개발자는 분명 사용자 제스처에서 play()를 호출했다고 생각하지만, 실제로는 play()가 브라우저 콜스택을 벗어난 이후(예: useEffect나 비동기 콜백)에 실행되어 activation 권한이 거부되는 상황을 보게 된다.

이를 피하려면 핵심 원칙은 단순하다. 미디어의 재생 허가를 얻어야 하는 호출은 반드시 사용자 이벤트 핸들러의 동기적 컨텍스트 안에서 실행해야 한다. 예를 들어 React에서 안전한 패턴은 다음과 같다.

- 이벤트 핸들러 안에서 직접 video.play()를 호출한다(가능하면 setState보다 먼저).
- 만약 렌더 상태를 먼저 바꿔야 한다면 react-dom의 flushSync로 렌더링을 동기적으로 강제한 뒤 즉시 play()를 호출한다.

#### 간단한 예시:

```javascript
// 권장: 이벤트 핸들러에서 직접 재생
function onTouch(e) {
  document.querySelector('video').play(); // 영상 먼저 재생 시키고
  
  setPlaying(true); // 그 다음에 상태 변경
}
```

#### 동기 렌더 강제 예시:

```javascript
import { flushSync } from 'react-dom';

function onStart(e) {
  flushSync(() => setShowing(true)); // 먼저 상태 변경을 동기적으로 반영
  
  videoRef.current?.play(); // 그 다음 같은 콜스택에서 재생 호출
}
```

작은 함정: pointerdown/touchstart 같은 더 이른 시점의 이벤트를 이용해 호출하면 transient activation을 확보할 수 있을거라 생각할 수도 있지만, 이 방법은 activation 활성화를 보장하지 않기 때문에 피하는 편이 좋다. 성능이 낮은 모바일 기기 환경에서는 특히 그렇다.


### 함정3. await와 setTimeout

이쯤 되면 당연하겠으나, await와 setTimeout이 동작시키는 코드는 transient activation이 이미 만료된 후에 동작한다. 따라서 transient user activation 권한이 필요하다면 위 react 예시에서 처럼 즉시 동작시켜야 한다. requestAnimationFrame도 중복하여 콜백을 호출하면 마찬가지로 transient activation이 만료된다.


### 함정4. transient user activation 소비

어떤 브라우저 동작이나 웹 API(예: 팝업 열기, 일부 경우의 media.play(), fullscreen 요청 등)가 사용자 활성화를 필요로 하고, 그 동작이 실행되면 브라우저는 그 활성화를 소비했다(consume)고 판단할 수 있다. 그 결과 같은 사용자 제스처로 이어지는 이후의 비권한 작업들은 더 이상 임시 활성화의 혜택을 받지 못한다. 즉, 활성화가 "한 번 사용되면" 다음 호출에는 적용되지 않을 수 있다.

만약 영상 자동 재생 권한이 필요하다면, 그 권한을 소모하지 않도록 주의하여 동일한 동기 컨텍스트에서 필요한 호출들을 처리해야 한다. 위에서 논한 대로 이 정책은 브라우저/버전별 차이가 있으니 실제 동작은 장치별로 반드시 테스트해야 한다. `navigator.userActivation.isActive`가 true인지 false인지를 확인하여 분기 처리를 해야할 수도 있다.


### 함정5. User Activation 권한이 없어도 잠시 영상의 Autoplay가 동작하는 현상

iOS 17 미만 버전에서는 User Activation이 없거나 이미 소비되어 버렸음에도 불구하고 잠시 영상이 Autoplay 된다. 하지만 곧 `NotAllowedError`가 발생하며 영상이 멈춘다. User Activation API가 지원되지 않는 iOS 16.4 미만 버전에서는 무슨 이유인지 확인할 길이 없어 어쩔 수 없지만, iOS 16.4는 UserActivation API가 명시적으로 지원되기 때문에 코드에서 Activation 유무를 확인하여 분기 처리를 함에도 불구하고 이렇게 동작한다.

원인은 내가 과거에 작성한 아래 글을 참고하여 확인할 수 있다.

[HTML Video element를 재생하려고 했더니 영상이 나오다가 만다. safari 버그가 아닐까? webkit 소스코드를 까보았다](https://lovemewithoutall.github.io/it/ios-safari-video-autoplay-breakdown/)

Webkit은 영상 재생을 promise로 일단 실행(많은 리소스가 필요하기 때문이다)하고, 동시에 activation flag 보유 여부를 확인하기 때문에 이런 현상이 발생하는 것이다.


## iOS 17 미만 버전에서는 어떻게 영상 자동 재생을 하면 좋은가?

경험 있는 개발자라면 위에서 열거한 여러 함정을 피하기란 불가능에 가깝다는 사실을 이미 깨달았을 것이다. 영상 자동 재생은 사실상 sticky vs transient user activation의 문제가 아니다. 이는 브라우저 내부 구현에 정의된 휴리스틱 정책의 문제다.

이 문제를 해결하기 위하여 나는 iOS 17 이상과 그 미만 버전을 분리하는 휴리스틱한 접근법을 제안한다. 그 이유는 다음과 같다.

앞서 설명했듯이 iOS 16 이하에서는 각 media elememt 별로 user activation 활성화 권한이 필요하다. iOS 17 이상에서는 해당 문서에 생성되어 있는 media elememt가 모두 user activation 활성화 권한을 부여 받는다. 

만약 iOS 17 미만 버전에서 틱톡과 같은 연속 재생 경험을 제공하려면, **단 하나의 `<video>` element만 생성하고 `src` 속성만 교체하며 재사용하는 방식(Single Video Element Pattern)**을 사용해야 한다. 사용자가 첫 번째 영상에서 재생 버튼을 눌러 권한을 획득했다면, 그 video element는 '축복받은(blessed)' 상태가 되어 이후 소스를 변경하더라도 소리 켜진 상태로 자동 재생이 가능하다. 반면, 매번 새로운 `<video>` 태그를 생성하여 DOM에 추가하는 방식은 각 태그마다 새로운 사용자 제스처를 요구하므로 연속 재생이 불가능하다.


## 결론

영상 자동 재생을 위하여 제스처는 반드시 필요하다. 하지만 제스쳐의 적용 범위는 다소 혼미하다. 웹에서의 Autoplay policy(영상 자동 재생 정책)은 표준 스펙보다는 브라우저 제조사의 정책과 구현에 더 큰 영향을 받는다. 특히 모바일 Safari의 경우 버전마다 동작이 다르다. '당연히 되겠지'라고 생각하면 안 된다. 다양한 기기와 버전에서의 테스트가 필요하고, 가끔은 Webkit 소스 코드를 직접 열어볼 필요도 있다.

영상 관련 개발자들의 건투를 빈다.

EOD

20251201
