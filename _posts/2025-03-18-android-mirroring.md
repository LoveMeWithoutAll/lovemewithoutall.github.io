---
layout: single
title: Scrcpy - Android 미러링
date: 2025-03-18 08:50:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/walking-android.jpg"
    image: "/assets/images/walking-android.jpg"
categories:
- IT
tags: [Android]
---

1. 설치
    1. macos: brew installl scrcpy
2. 안드로이드 기기 연결
    1. usb로 mac과 안드로이드 기기 연결
    2. 안드로이드 기기에서 usb 디버깅 On
    3. 정상 연결 확인: adb devices
3.  터미널에서 명령어 입력; scrcpy

## adb devices가 실행되지 않는다면(MacOS 기준) 트러블 슈팅

1. 안드로이드 기기를 usb에 꽂았다 빼서 기기 접근 권한 부여
1. 그래도 안 되면 System Setting > Privacy & Security > Full Disk Access > 터미널 앱에 권한 부여
1. adb 다시 실행: adb kill-server && sudo adb start-server && adb devices
1. 그래도 안 되면 안드로이드 연결 도구 재설치 brew install android-platform-tools android-file-transfer
1. scrcpy도 재설치 brew install scrcpy
1. adb 재실행 하고: adb kill-server && sudo adb start-server && adb devices
1. scrcpy 실행

20250318

edit: 20250502
