---
layout: single
title: web game frontend 개발 POC(11키티즈)
date: 2025-06-18 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://t1.daumcdn.net/thumb/R1280x0/?fname=http://t1.daumcdn.net/brunch/service/user/f5ma/image/DLGpTKW3EicPuw4iYtl1Cg-ZP_4.gif"
    image: "https://t1.daumcdn.net/thumb/R1280x0/?fname=http://t1.daumcdn.net/brunch/service/user/f5ma/image/DLGpTKW3EicPuw4iYtl1Cg-ZP_4.gif"
categories:
- IT
tags: [web game]
---

11키티즈 개발을 위하여 frontend에서 POC를 수행하고 그 결과를 정리했다(2024년 초 수행).

# 테스트 대상

* 11키티즈: 다마고치처럼 고양이를 키우는 웹 게임
* 환경: iOS & Android mobile application의 webview
* 기능 요구 사항: 게임 속 캐릭터인 고양이와의 상호 작용이 가능해야 한다. 고양이는 사용자의 동작에 따라 애니메이션과 소리로 반응해야 한다.
* 비기능 요구 사항:
    * 게임의 퀄리티를 위하여 [고품질의 이미지](https://brunch.co.kr/@11design/19)와 애니메이션(lottie 혹은 PNG sequence로 구현)이 필요
    * 캐릭터와 상호 작용시, 애니메이션 로딩 등으로 인한 지연이 없어야 한다.

# 11키티즈 프로토타입 성능 테스트 결과

## 요약
* 빠름
* cpu와 배터리 사용량에 유의 필요

## 테스트

### 테스트 방법
* 크롬 브라우저 퍼포먼스 탭
* 크롬 브라우저 레이어 탭
* 크롬 리액트 프로파일러
* 사파리 브라우저 타임라인 탭
* 사용 기기: Mac Pro M1 + 저사양 모바일 기기

### 성능 문제 없음
* LCP: 200ms 이하
* 렌더링 완료: 200ms 이하
* FID: 그림 최초 다운로드시 45ms 이하
* cpu 쓰로틀 x6이어도 구동에 문제 없음
* layer
    * 고양이 & 로띠 & 배경 레이어 분리는 브라우저 구현에 따라 다름
        * 크롬: 레이어 모두 분리
        * 사파리: 로띠 레이어만 분리
            * 로띠 레이어 분리 사유: css 3d transform

## 주요 병목 지점

### 최초 로드
* 현상: 유일한 main thread long task(100ms 이하)
* 원인: js, png 파일 리소스 다운로드 및 스크립트 실행
* 일반적인 기준으로 보아 빠름. 추후 코드가 추가될수록 무거워질 예정

### 고양이 감정 바뀌면, 고양이 깜빡임 현상 발생
* 원인: 고양이 감정 png 파일 다운로드
* 대책: 고양이 감정 png 파일 미리 pre-download

## 특이사항

### 그 외 최적화
* 현상: 로띠가 동작하면 고양이 컴포넌트도 렌더링 된다
* 원인: 로띠를 고양이 컴포넌트 내부에서 제어해서 발생하는 문제
* 조치: 로띠 컴포넌트와 고양이 컴포넌트의 분리
    * 마크업 작업시 UI팀에 요청 필요

### 에너지 소모
* Energy Impact: High
* 기준: 사파리 브라우저
* Average CPU: 38.7% (권장 사용량 3%)
* 배터리 소모율과 직결
* 원인
    * png animate에 cpu 자원 다량 소모
    * 로띠는 off시 약 2% 정도의 cpu 사용량 차이 발생

# 대응
* 최초 로딩 시간
    * 브라우저 캐싱 적용
    * 게임 중 이미지 로딩을 하지 않기 위해, 최초 접속 시 복수의 고품질 이미지 파일을 다운로드 필요 -> 최초 로딩 시간 불가피
    * 이미지 다운로드 시간 확보를 위하여 게임 시작시 로딩 화면을 띄운다
* 애니메이션
    * 캐릭터의 움직임은 **PNG sequnece + CSS @keyframes**로 처리
    * 특수 효과는 Lottie 사용
* 고품질 이미지 다운로드
    * 캐릭터 애니메이션마다 각각 약 10MB의 크기
    * 캐릭터마다 필요한 애니메이션은 최소 6개
    * PNG 파일의 이미지 최적화([tinypng](https://tinypng.com/) 사용)로 파일 용량을 각각 약 2~3MB 수준으로 낮춤

# 결론
* 고품질 애니메이션 + 실시간 상호 작용”이라는 까다로운 요구 사항을 모바일 WebView에서도 충분히 달성할 수 있음

EOD

20250618
