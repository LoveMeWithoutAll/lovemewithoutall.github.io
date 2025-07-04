---
layout: single
title: requestAnimationFrame의 callback 함수 실행 시점 - 두 번째 rAF에서야 진짜로 화면에 그려졌다고 보장할 수 있는 이유
date: 2025-07-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
    image: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
categories:
- IT
tags: [javascript]
---

requestAnimationFrame의 콜백 함수 실행 시점이 자꾸 햇갈려서 정리해보았다.

## 환경
- iOS webview

## 현상

iOS 11번가 앱의 웹뷰에서 풀스크린 쇼츠 플레이어(정식 이름은 11Play)를 이탈(URL 이동)할 때, `<video>`가 잠깐 검은 상자로 깜빡이는 문제를 잡기 위해

```ts
showImage();                    // 비디오 위에 썸네일 DOM 삽입

return new Promise((resolve) => { // Promise가 resolve 되면 플레이어 이탈 허용
  requestAnimationFrame(() =>     // rAF #1
    requestAnimationFrame(resolve)  // rAF #2
  );
})

```

처럼 **rAF 두 번 대기** 패턴을 썼다.

“한 번만 기다려도 되지 않나?” 싶지만, **렌더링 파이프라인의 정확한 순서**를 보면 두 프레임을 기다려야 하는 이유가 분명해진다.

---

### 1. 한 프레임(대체로 60 Hz) 안에서 브라우저가 하는 일

```text
VSyncₙ                                                
① rAF 큐 실행            <- rAF #1                    
② Style 계산                                          
③ Layout                                              
④ Paint                                               
⑤ Commit (메인 → 컴포지터 스레드 복사)                      
⑥ Composite / Draw (GPU 커맨드 제출)                     
( GPU swap 예약 )       <- 여기까지 끝나야 ‘화면 픽셀’ 확정    
-----------------
VSyncₙ₊₁     ← 패널이 프레임을 교체
① rAF 큐 실행            <- rAF #2
... 반복
```

* **rAF 콜백**: 앞선 VSync가 끝난 직후, 새 프레임을 그리기 위한 **렌더링 파이프라인 시작 직전**
* **Style -> (...) -> Composite**: rAF 뒤에 연속으로 실행.
* **화면 표시**: 다음 VSync 시점에 GPU에서 완성된 버퍼로 디스플레이 패널에 보낼 데이터를 스왑.

---

### 2. rAF #1에서는 아직 “픽셀이 GPU에 없다”

| 시점                   | 무엇이 끝났나?        | showImage() 함수가 의도한 DOM 변경이 어디까지 반영됐나? |
| -------------------- | --------------- | ---------------------------- |
| **rAF #1 진입**        | 아무것도 렌더링 안 됨    | **단순히 “DOM 플래그”만 설정**            |
| **Style → Paint**    | 레이아웃·픽셀 버퍼 생성   | CPU 메모리에 그림이 생김              |
| **Commit**           | 레이어 트리 컴포지터로 복사 | GPU까지 아직 안 감                 |
| **Composite / Draw** | GPU 명령 제출       | 이제야 GPU 버퍼 업데이트              |
| **VSync₁**           | 버퍼 스왑           | **사용자·탭 스냅샷에 보임**                |

따라서 **rAF #1** 안에서 `resolve()`로 탐색을 허용하면,
iOS의 WKWebView가 URL 이동을 위해 탭 스냅샷을 찍을 때, **렌더링 파이프라인의 Commit 시점 이전에 스냅샷**을 찍어서 검은 사각형이 재발한다.

---

### 3. rAF #2는 “레이어 커밋까지 끝난 후” 호출된다

* rAF #2가 실행되려면 이미 **Composite → GPU swap 예약**이 끝나 브라우저가 다음 VSync만 기다리는 상태여야 한다.
* 즉, rAF #2 실행 시점에는 이미 `showImage()`가 만든 픽셀이 **GPU 버퍼에 존재**함이 보장된다.
* 이 시점에 URL 이동·스냅샷·Process Swap이 시작돼도 **검은 비디오 레이어가 아닌 showImage 함수가 띄운 이미지**가 캡처된다.

---

### 4. 그러니까 rAF는 언제 실행되냐면

```text
VSync₀
└─ rAF #1 ─ showImage(); resolve();  ← 여기서 URL 이동(플레이어 이탈)을 허용하면 위험
   렌더링 파이프라인의 그 과정(Style→ ... →Composite)
VSync₁  ← 썸네일 픽셀이 실제로 보이는 순간
└─ rAF #2 ─ resolve();              ← 여기서 URL 이동 허용 = 안전
   그 과정(Style→ ... →Composite)
VSync₂                              ← 이 시점에는 다른 URL 페이지로 이동 시작
```

---

### 5. 렌더링 파이프라인 순서 다시 정리

| #                                             | 실제로 일어나는 일                                                                                                       
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------
| **0. VSync₀**<br>이전 프레임 표시 완료                 | 디스플레이가 수직 공백(Vertical Blank)으로 진입. OS/브라우저에 “다음 프레임 그릴 시간” 알림(VSYNC)                                             |                     
| **1. `showImage()` 실행**                       | DOM 변경만 기록. **스타일·레이아웃 계산은 아직 안 됨**                                                                              |                     
| **2. rAF 콜백 실행** (첫 rAF)                      | rAF는 **VSync₀ 직후·다음 렌더링 **직전** 단계에 실행**<br> -> `resolve()`를 여기서 호출하면 이 시점에 아직 렌더링 파이프라인(3-6)이 *안 끝난* 상태 
| **3. Style → Layout → Paint** | 한 프레임 안에서 브라우저가 DOM 변경을 계산·픽셀화                                                                                   |                     
| **4. Commit**                       | 메인 스레드 → 컴포지터 스레드로 레이어 트리 복사                                                                                     |                     
| **5. Composite / Draw**          | 컴포지터 스레드가 여러 레이어를 합성하고 GPU에 커맨드 제출                                                                               |                     
| **6. Buffer swap 예약**                         | GPU가 “다음 VSYNC에 버퍼 교체” 예약                                                                                        |                     
| **7. VSync₁**                       | 이번에 만든 프레임이 **이 순간** 패널로 전송돼 사용자에게 보임 <br> -> 여기서야 비로소 `showImage()` 결과(이미지) 등장                                    |                     
| **8. rAF 콜백 실행** (두 번째 rAF)        | VSync₁ 직후. 이제 직전 프레임이 **완전히 화면에 반영**됐음을 보장                                                                       |                     
| **9. Style → Layout → … → VSync₂**   | 다음 프레임 렌더링 사이클 반복                                                                                                   |                     


---

## 2줄 요약

requestAnimationFrame의 콜백은 1VSync 늦게 실행되는 게 아니다. 이번 렌더링 사이클에 당장 실행된다. 

**참고자료:** 
- [Why is requestAnimationFrame better than setInterval or setTimeout(stack overflow)](https://stackoverflow.com/questions/38709923/why-is-requestanimationframe-better-than-setinterval-or-settimeout)
- [RenderingNG 아키텍처](https://developer.chrome.com/docs/chromium/renderingng-architecture?hl=ko)

EOD

20250704
