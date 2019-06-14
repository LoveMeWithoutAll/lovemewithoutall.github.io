---
layout: single
title: IBM Cloud에 Node.js로 만든 텔레그램봇 배포하기
date: 2018-06-25 23:50:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/c/c7/IBM_Bluemix_logo.svg/1200px-IBM_Bluemix_logo.svg.png"
categories:
- IT
tags: [chatbot, telegram bot, IBM Cloud]
---

## 0. 클라우드 난민

한동안 [네이버 클라우드 플랫폼](https://www.ncloud.com)의 마이크로 서버를 무료로 잘 쓰고 있었는데, 2018년 6월 1일자로 유료화 되는 바람에 더이상 서버를 유지할 수 없었다. 불과 한 달 13000원이라지만 아무튼 나는 가난한 개발자다... 그래서 이번 기회에 무료로 클라우드 서버를 제공하는 [IBM Cloud](https://console.bluemix.net/)로 서버를 옮겼다. 하고 많은 클라우드 서비스 중 IBM을 선택한 이유는...
 
 1. 무료(이게 가장 중요)
 1. [cloud foundry]를 사용할 수 있어 편리함
 1. 예전에 이름이 BlueMix였던 시절에 써봄

 가 되겠다.

## 1. 배포할 서비스
이번에 올릴 서비스는 [@hangifbot](https://github.com/LoveMeWithoutAll/HanGifBot)이다. Node.js로 만든 텔레그램 챗봇인데, [Microsoft Cognitive Service Bing Image Search API](https://azure.microsoft.com/ko-kr/services/cognitive-services/bing-image-search-api/)(이름 한 번 길다)를 사용해 gif 이미지를 검색해주는 인라인봇이다. 카카오톡의 샵검색 비슷한 모양새다. 예전에는 텔레그램이 기본 지원하는 인라인봇(@gif)이 한글 검색을 지원하지 않는 불편함이 있었는데, 이를 해결하고자 만들었던 봇이다. 지금은 @gif봇이 한글 검색을 지원하는 덕분에 효용이 예전만 못하지만, 그래도 bing 검색이 된다는데 의의가 있다고 보아 살려두었다.

## 2. 서버 만들기

나는 [cloud foundry] 애플리케이션으로 Node.js 서버를 띄웠다. 방법은 다음과 같다.

1.**리소소 작성** 을 클릭하고

![create-resource](/assets/images/2018-06-25-deploy-on-ibm-cloud/create-resource.png)

2.**Cloud Foundry 앱** 중 SDK for Node.js를 선택한다. 

![create-cloud-foundry-app](/assets/images/2018-06-25-deploy-on-ibm-cloud/create-cloud-foundry-app.png)

이제 서버가 만들어졌다.

![after-created](/assets/images/2018-06-25-deploy-on-ibm-cloud/after-created.png)

## 3. 설정하기

Cloud Foundry 앱을 사용하기 위해서는 앱의 루트 경로에 *manifest.yml* 파일을 만들어 설정을 해줘야 한다. 깊게 설명할 처지가 못되어 간단한 예제 파일만 올린다.

```bash
# manifest.yml
applications:
- name: hangifbot
  instances: 1
  memory: 128M
  disk_quota: 1024M
  no-route: true  # 이 앱은 라우터 필요 없다
```

## 4. 배포하기

~~[공식 문서](https://console.bluemix.net/docs/starters/upload_app.html)를 참조하여 배포해보자.~~(링크 깨짐)

1.**IBM Cloud Develop Tools** 설치

먼저 [IBM Cloud Develop Tools](https://console.bluemix.net/docs/cli/index.html#overview)를 설치한다. [cloud foundry] 앱으로 만든다더니 웬 IBM이냐 생각할 수 있겠지만, IBM Cloud Develop Tools는 [Cloud Foundry cli](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)의 확장판이니 걱정말고 설치하면 된다. 설치에는 시간이 좀 걸린다.

2.배포

우선 내 로컬 머신에서 서비스가 잘 돌아가는지 확인하자. 문제없이 돌아간다면 준비 끝이다. 이제 커맨드창을 띄워 배포할 서비스가 있는 위치로 이동한 후, 다음과 같이 명령어를 입력한다. ~~IBM Cloud cli를 설치했는데 왜 *bluemix*를 명령어로 치냐면, *bluemix*가 IBM Cloud의 예전 이름이기 때문이다.~~(2019.06.13 현재 bluemix 명령어는 더이상 사용할 수 없다)

```bash
# region에 따라 달라지지만 light 플랜(공짜)라면 다 ng(미국 남부)일거다
# bluemix api https://api.ng.bluemix.net
# 2019.06.13. 위 명령어는 이제 안해도 된다

# ID는 본인의 메일 계정으로 넣자
# bluemix login -u youngseon.me@gmail.com # 2019.06.13. 사용 불가 명령어
ibmcloud login

# ID/PW를 입력한다

# bluemix 대신 bx만 쳐도 된다. -> (2019.06.13. 이제 안됨)
# 커맨드창에서 현재 위치한 곳에서 돌아가는 앱을 어디다 올릴건지(타겟을 어디로 할건지) 정해준다.
# bx target --cf # 2019.06.13. 사용 불가 명령어
ibmcloud target --cf
```

이제 IBM Cloud가 시작 가이드가 알려주는대로 이 명령어를 입력하면

```bash
# bx app push hanGifBot
ibmcloud cf push
```

# 이제 잘 돌아간다! 끝!

## 아래는 과거 bluemix 시절의 이야기다.
## ibmcloud를 쓰는 2019.06.13. 현재는 health check, route 모두 따로 설정해줄 필요 없다

잘 올라가는듯 하더니

![before-failed](/assets/images/2018-06-25-deploy-on-ibm-cloud/before-failed.png)

이런 오류가 뜬다.

![start-unsuccessful](/assets/images/2018-06-25-deploy-on-ibm-cloud/start-unsuccessful.png)

원인은 내가 지금 올릴 **앱이 챗봇이기 때문**이다. 시작 가이드가 알려주는 위 명령어는 웹앱을 위한 명령어다. 그러므로 우리는 이렇게 해야한다.

```bash
# health check 끄고
bx cf set-health-check hanGifBot none

# route도 끄고 배포한다
bx cf push --no-route
```

이제 성공했다.

![deploy-success](/assets/images/2018-06-25-deploy-on-ibm-cloud/deploy-success.png)

## 5. 마무리

IBM Cloud를 사용하면, 복잡한 설정을 없이 로컬 서버에서 돌아가는 대로 *cf push*만 날려서 클라우드에 내 앱을 배포할 수 있다(공짜로). *npm install*은 언제 하나 걱정할 것 없다. 알아서 해준다. 끝!

## 참고

1. 이 글은 [Deploy Node.js telegram bot on IBM BlueMix](https://lovemewithoutall.github.io/it/deploy-bot-on-IBM-bluemix/)의 업그레이드판입니다.
~~1. [ibm cloud developer tools CLI] 1.3.3 버전 기준입니다.~~

[cloud foundry]: https://console.bluemix.net/
[ibm cloud developer tools CLI]: https://www.ibm.com/cloud/cli