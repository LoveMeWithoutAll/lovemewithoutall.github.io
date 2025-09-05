---
layout: single
title: 모바일 기기 webiew에서의 video 최적화 - Hardware overlay
date: 2025-09-06 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/docs/chromium/videong/image/diagram-rendering-flow-57e1870f47599_1920.png"
    image: "https://developer.chrome.com/static/docs/chromium/videong/image/diagram-rendering-flow-57e1870f47599_1920.png"
categories:
- IT
tags: [video]
---

모바일 기기는 저성능이다. 모바일 웹브라우저는 저성능이다. 웹뷰는 더더욱 저성능이다. 그러니 개발자에게 웹뷰에 올라갈 어플리케이션의 성능 문제는 너무나도 중요하다. 그러나 성능 문제는 언제나 쉽지 않다. 어렵다. 웹뷰에는 근본적인 한계가 있다. 모바일 기기는 OS 차원에서 웹뷰의 성능에 제한을 둔다. 단순히 엔진 자체가 느리기 때문이 아니다. 단순히 기기 성능 때문도 아니다. 웹뷰는 성능보다 안정성이 우선이기 때문이다. 이 점에 대해서 공식 문서는 없다. 다만 내가 지난 십 수 년 동안 개발하며 느낀 점이다. 

웹뷰에 영상을 띄우려면 더더욱 골치 아프다. 가뜩이나 느려 터진 웹뷰에다가 영상 출력이라는 무거운 작업을 올린다고?  2025년 현재를 기준으로, 불과 몇 년 전까지만 해도 이건 사실상 불가능한 작업이었다. 거대한 영상 파일을 빠르게 다운로드 하는 동시에, 기기가 영상 파일을 디코딩 하여 화면의 vsync에 맞춰 출력하면서, 사용자 제스처에 부드럽게 반응하고 이런 저런 기능을 돌린다는 건 2025년 현재의 모바일 기기조차도 쉬운 일이 아니다. 수 없이 많은 모바일 앱이 출시되던 2010년대 초중반 당시, 앱스토어에 쏟아져 들어오는 앱 만큼이나 다양한 유형의 앱이 있었다. 그 중에서 왜 틱톡 스타일의 UX를 가진 앱이 없었는지 생각해 보았는가? 그 시절에 틱톡 같은 앱을 만든다는 아이디어가 있었다면 큰 돈을 벌지 않았을까? 내 생각에 그건 그냥 불가능한 일이었다. 그 시절의 기기 성능으로는 영상을 무한 피드로 재생하는 작업은 불가능했다. 그러니까 한 마디로, 영상은 무겁다는 것이다.

하지만 현 시점에서 웹뷰가 영상을 다루기 위한 해법이 없는 것은 아니다. 모바일기기 하드웨어가 영상을 화면에 바로 쏘게 하는 것, 바로 hardware overlay 다. 이 방식에서는 영상을 디스플레이 엔진(안드로이드는 Hardware Composer-줄여서 HWC, iOS는 Core Animation + display controller)에 직접 바로 얹어 출력한다. 영상을 메모리에 복사하는 등 등 중간 작업을 대부분 건너 뛰니까 오버헤드가 크게 줄어들고, 따라서 기기 리소스 소모가 크게 줄어든다. 이러면 자바스크립트 메인스레드에 리소스를 보다 여유롭게 할당할 수 있으니, 사용자 제스쳐 처리 등 다른 작업을 할 여유가 생기는 것도 물론이다.

그러면 hardware overlay가 있으니 모든 문제를 해결할 수 있을까? hardware overlay를 하면 여러모로 좋겠지. 리소스가 확실히 덜 든다. 하지만 hardware overlay를 위한 경로는 매우 쉽게 깨진다. 영상을 js나 css로 조작하는 짓을 하면 hardware overlay plane을 타지 않는다. 예를 들어 보자.  영상 위에 opacity 0.5로 불투명한 엘리먼트를 띄운다고 하면, 그 엘리먼트 뒤로는 흐릿하게 영상이 보여야겠지? 그러면 무조건 GPU composite도 필요하고, 소프트웨어적으로 연산을 해줘야 한다. 이러면 hardware overlay 못 하는거다. 깨지는거다. 그 뿐만이 아니다. 영상에 transform을 하거나 등등, hardware overlay 이걸 적용하려면 사실상 영상 위에 아무 UI 엘리먼트도 없어야 한다. 그렇지 않으면 대개 hardware overlay가 깨진다.

hardware overlay를 사용하는 게 목적이라면, hardware overlay가 되고 있는지 반드시 확인해야 한다. 모바일 기기가 OS 차원에서 혹은 플랫폼 차원에서 FE개발자의 의도대로 동작하도록 강제하는 것은 사실상 불가능하기 때문이다. 기기 리소스 관리는 OS의 영역이고, FE개발자는 브라우저(혹은 웹뷰)를 통해 OS(혹은 플랫폼)에게 협조를 구할 수 있을 뿐이다.

하지만 hardware overlay가 적용되었는지 살펴보는 간단한 방법은 사실상 없다. 웹뷰가 들어갈 네이티브 앱을 직접 로컬 빌드해서 확인하는 편이 빠르다. 하지만 현실 세계에서는 모든 협업이 원활하게 진행될 수 없다. 만약 사정상 FE개발만 붙잡고 있어야 하고, 앱 개발자와의 협업이 쉽지 않다면, 혹은 밤새 개발하고 있는데, 문득 주변을 둘러보니 사무실에 나 혼자 라면 어쩔건가?

첫번째 방법은 시뮬레이터의 모바일 브라우저를 활용하는 것이다. iOS를 기준으로 살펴보자. Xcode simulator를 열고, 가상 아이폰에서 사파리를 띄워 웹어플리케이션에 접속, 그리고 메뉴에서 Xcode > Debug > Color blended layers를 활성화 하고 확인해보자. iOS 18 기준으로 실 기기에서는 이 기능이 없으니 시뮬레이터를 사용할 수 밖에 없다. 아무튼 가상 아이폰 화면에서 빨간색 레이어는 모두 hardware overlay가 안 되고 있는 것들이다. 빨간 표시는 알파 블렌딩을 하고 있다는 의미인데, 알파 블렌딩이란 앞 뒤 픽셀을 섞어서 화면에 뿌릴 최종 색을 정하는 작업이다. 즉 계산을 추가로 한 후에야 화면에 영상을 뿌리는 게 가능한 레이어라는 뜻이고, 그러니 hardware overlay가 불가능 한 것이다.
￼
![hardware-overlay-fail](/assets/images/2025-09-05-hardware-overlay/hardware-overlay-fail.png)

위 예시는 내가 개발한 11번가 숏폼 플레이어 웹앱이다. 플레이어 전체가 피를 흘리고 있는 것처럼 보인다. 영상 위 아래를 그림자로 덮었기 때문이다. 물론 그림자는 CSS opacity로 구현되었다. 그야말로 hardware overlay 최적화의 반례 그 자체라 할 수 있다. 개발자 입장에서는 몸이 찢겨져 나가는 고통이었으나, 시청자가 화면에 집중하도록 해야 한다는 디자인 스펙을 바꿀 수는 없었다.

두번째 방법은 로그 확인이다. Xcode instruments를 활용하라. 

![xcode gpu log](/assets/images/2025-09-05-hardware-overlay/xcode%20gpu%20log.png)

이건 Xcode instruments 화면. 얼핏봐도 무섭지 않은가? 나도 그렇다

영상을 틀어두고 GPU 로그를 확인하면 hardware overlay가 되고 있는지 대강 추정할 수 있다. GPU 로그를 보면…

```
Channel Name	Creation	Duration	State	CPU to GPU Latency	Frame	Label	IOSurface Accesses	
Vertex	00:01.136.396	675.88 µs	Active	737.71 µs	Frame 42	coreanimation.command-buffer:coreanimation.assembly-encoder  (com.apple.WebKit.GPU (441)) 0xb3c4145		
Fragment	00:01.136.396	668.25 µs	Active	387.96 µs	Frame 34	Read Surface: 184 408 163 192 165 315 186 -> Write Surface: 32  (backboardd (71)) 0xb3c414d	Read Surface: 184(1179×687:r03w) 408(1179×1536:8a3b) 163(1179×720:8a3b) 192(849×320:8a3b) 165(264×798:8a3b) 315(1179×107:8a3b) 186(1179×168:r03w) -> Write Surface: 32(1179×2556:83b&)	
Fragment	00:01.137.072	75.38 µs	Active	1.41 ms	Frame 42	coreanimation.command-buffer:coreanimation.assembly-encoder  (com.apple.WebKit.GPU (441)) 0xb3c4144	Write Surface: 319(1179×1536:8a3b)	
Vertex	00:01.137.072	39.12 µs	Active	1.41 ms	Frame 42	coreanimation.command-buffer:coreanimation.assembly-encoder  (com.apple.WebKit.GPU (441)) 0xb3c4148		
Vertex	00:01.137.112	27.79 µs	Active	1.45 ms	Frame 42	coreanimation.command-buffer:coreanimation.assembly-encoder  (com.apple.WebKit.GPU (441)) 0xb3c414b		
Fragment	00:01.137.147	33.83 µs	Active	1.49 ms	Frame 42	coreanimation.command-buffer:coreanimation.assembly-encoder  (com.apple.WebKit.GPU (441)) 0xb3c4147		
Fragment	00:01.137.182	60.67 µs	Active	1.52 ms	Frame 42	coreanimation.command-buffer:coreanimation.assembly-encoder  (com.apple.WebKit.GPU (441)) 0xb3c414a	Write Surface: 319(1179×1536:8a3b)	
Vertex	00:01.152.957	16.96 µs	Active	458.42 µs	Frame 35	  (backboardd (71)) 0xb3c415c		
Fragment	00:01.152.974	168.54 µs	Active	475.75 µs	Frame 35	Read Surface: 184 408 163 316 315 -> Write Surface: 33  (backboardd (71)) 0xb3c415b	Read Surface: 184(1179×687:r03w) 408(1179×1536:8a3b) 163(1179×720:8a3b) 316(849×320:8a3b) 315(1179×107:8a3b) -> Write Surface: 33(1179×2556:83b&)	
```

와 같이 알 수 없는 문자로 가득하다. 하지만 면벽 수행하듯 뚫어져라 쳐다보면	 뭔가 패턴이 보이기 시작한다. 나의 발견을 정리하면 이렇다(관련된 공식 문서는 찾지 못했다).

```
hardware 오버레이 실패일 때
Frame N:
   Read Surface: (비디오 프레임 1179×720)
   Read Surface: (UI 버튼 200×50)
   Read Surface: (텍스트 바 1179×107)
                ↓
   Write Surface: (최종 화면 1179×2556)
```

```
hardware 오버레이 성공일 때
Frame N:
   Read Surface: (UI 버튼 200×50)
   Read Surface: (텍스트 바 1179×107)
                ↓
   Write Surface: (최종 화면 1179×2556)
```

그러니까 READ 로그에 비디오 프레임 크기(1179×720 같은 값) 이 연속적으로 나타나면 오버레이 실패인 것이다.
1. 비디오 크기 Read Surface가 로그에 보인다 → GPU 합성 (오버레이 안 됨)
2. 비디오 크기 Read Surface가 안 보인다 → hardware overlay 됨 (직접 출력)

hardware overlay가 순조롭다면
* GPU는 UI만 합성하고,
* 비디오는 최종 화면에 GPU가 아닌 디스플레이 엔진(HWC) 이 직접 얹어버림.
* 그래서 로그에 비디오 프레임 Read Surface가 안 보임.

인 것이다.

이렇게 로그를 보니 앞서 말한 hardware overlay를 위한 주의 사항이 더욱 명확해진다. 비디오 위에 어떤 UI라도 겹치는 순간, iOS 사파리/WKWebView에서 “hardware overlay(직접 합성)”은 사실상 깨진다.

그러면 11번가 숏폼 플레이어 같은 앱은 어떻게 해야 하는가? 이는 깊고 심오한 주제이기에 이 글에서 간단히 말할 수 없는 주제다. 다만 최적화 목표를 ‘hardware overlay 유지’가 아니라 ‘GPU 합성 경로를 최대한 가볍게’로 바꿔야 한다, 정도만 말해 두겠다.

지금까지 iOS를 기준으로 살펴보았는데, Android webview는 다른가? VideoNG고 뭐고… iOS 보다는 조금 더 덜 엄격하게 overlay 적용 가능 여부를 검사하니 사정이 낫지만… 크게 다르지 않다. 영상 위에 CSS로 transform 혹은 opacity를 조작하거나, 영상을 UI 엘리먼트로 가리면 대개 hardware overlay 실패다. 

그러면 웹뷰만으로는 영상 위에 UI 띄우면서 hardware overlay를 유도하는 건 절대 불가능한가? 예외적으로 완전히 불투명한 사각형 요소로 UI를 가리는 거라면 성공할 여지가 있기는 하다. 하지만 이건 뜻대로 동작하지 않을 수도 있다. 그러니 그냥 행운을 비는 수 밖에 없다.

이로서 틱톡 UX를 완전한 웹뷰만으로 구현하면 안 되는 이유가 늘었다.

### 참고

Chromium video NG: https://developer.chrome.com/docs/chromium/videong

EOD

20250906
