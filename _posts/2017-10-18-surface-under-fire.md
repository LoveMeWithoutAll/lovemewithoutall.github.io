---
layout: single
title: 서피스 프로3 포맷 삽질기
date: 2017-10-18 09:07:30.000000000 +09:00
header:
  teaser: "/assets/images/surface_broken.jpg"
  image: "/assets/images/surface_broken.jpg"
type: post
categories:
- IT
tags: [Surface Pro3, format]
---

마이크로소프트 서피스 프로3(이하 서피스)의 OS를 재설치 할 일이 생겼다. 서피스는 편리하게도 다양한 재설치 및 초기화 옵션을 제공한다. 하지만 나는 그런걸 믿지 않는다. windows95 시절에 하드플로피 디스크 30장을 동원, 백업 후 포맷을 했다가 다 날려먹은 뼈아픈 기억 때문이다.

![floppy disk](/assets/images/floppy-disk.jpg)
배드 섹터의 추억...

나는 이런 일에는 결단이 빠르다. 당장 [DiskPart]로 clean을 때려버리니, 서피스는 잠시나마 안식의 세계로 돌아갔다. 그리고 늘 하던대로 windows10을 다시 설치하기로 했다. 서피스의 경우는 windows 클린 설치가 타 기기보다 조금 까다로운 점이 있는데, 작업 순서대로 정리하자면 다음과 같다.

1. windows 설치 USB는 fat32로 포맷한다.
2. USB에 넣을 windows 설치 파일은 [공식 페이지](https://support.microsoft.com/ko-kr/surfacerecoveryimage)에서 다운받는다.
3. 다운받은 이미지를 그대로 USB에 복사해 넣는다.
4. UEFI 설정을 다음과 같이 변경한다.
 - 부팅 순서는 USB > SSD 로 한다.
 - secure boot control 을 disable 한다.
5. USB를 서피스에 끼운다. 
6. 부팅시, 서피스 로고와 함께 화면이 빨갛게 물들더라도 놀라지 않는다(secure boot control 설정 변경 때문이다).
7. 잘 안되면 [Surface 복원 또는 초기화 공식문서]를 참고한다.

위에서 조금 까다롭다고 했지만 windows95라든가 다른 오래된 OS를 생각하면 별거 없다. 분명 모든 작업이 순조로워야 할터인데, 내게는 유독 이런 에러가 발생했다.

![windows-boot-manage-install-error](/assets/images/windows-install-error.jpg)

text로는 이런 메세지다.
```
Windows failed to start.  A recent hardware or software change might be the cause.  To fix the problem :
1. Insert your windows installation disk and restart your computer.. 
2. 3. blah blah blah..

File:  \EFI\Microsoft\Boot\BCD
Status:  0xc000000f
Info: The boot configuration data for your pc is missing or contains errors. 
```

위 문제에 부딪히자 무슨 짓을 해도 다음 단계로 넘어갈 수 없었다. 구글링을 나오질 않는다. 적어도 한국어 웹에서는 그렇다. 그래서 영어로 검색해봤다. 검색어는 `surface Pro3 0xc000000f boot bcd`. 대번에 나온 [QnA글](https://answers.microsoft.com/en-us/windows/forum/windows_10-update/reset-surface-pro-3/f587ee8f-6a68-45fb-ac9e-6cc89c9936a1)이 있는데, 요약하자면 다음과 같다.

> ## MicroSD카드를 빼라!

나처럼 불필요하게 인생을 낭비하는 한국인이 없길 바라며 이만 줄인다. [_ K], you saved my life!

[DiskPart]: <https://technet.microsoft.com/ko-kr/library/cc766465%28v=ws.10%29.aspx?f=255&MSPPError=-2147217396>
[Surface 복원 또는 초기화 공식문서]: <https://support.microsoft.com/ko-kr/help/4023533/surface-restore-or-reset-surface>
[_ K]: <https://answers.microsoft.com/en-us/profile/bb629687-c2c5-4168-a6e0-263237695c37>