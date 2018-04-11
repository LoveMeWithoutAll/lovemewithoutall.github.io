---
layout: single
title: aws cli 초기 설정
date: 2018-04-03 13:07:30.000000000 +09:00
header:
  teaser: "https://d1.awsstatic.com/Test%20Images/Kate%20Test%20Images/Serverless_Web_App_LP_assets-01.998a9201f50434767e58269c5b71b485014a4531.png"
  image: "https://d1.awsstatic.com/Test%20Images/Kate%20Test%20Images/Serverless_Web_App_LP_assets-01.998a9201f50434767e58269c5b71b485014a4531.png"
type: post
categories:
- IT
tags: [AWS, aws-cli, IAM]
---

AWS의 [서버리스 웹 애플리케이션 구축](https://aws.amazon.com/ko/serverless/build-a-web-app/) 튜토리얼을 따라하고 있다. AWS를 내가 직접 만지는건 3년만이다. 그간 모르긴 몰라도 많이 바뀌었다. aws cli조차도 구성하기 힘들었다. 튜토리얼을 따라하기 위하여 aws cli로 S3를 만질 수 있도록 설정하는 과정을 정리해보았다.

## 1. aws configure

튜토리얼이 알려주는대로 aws cli를 설치한 후, 가벼운 마음으로 첫번째 명령어를 입력했다. **aws sync**. 아래와 같이 보기좋게 에러가 떨어진다.

```bash
$ aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website s3://wildrydes-ys-a --region ap-northeast-2 # 복사하려고 했더니
fatal error: Unable to locate credentials # 인증이 안된다는 오류가 뜨네
```

aws cli가 내 S3 bucket에 접근하기 위해서는 로그인 정보가 필요하다. 설정해주자. 우선 IAM에서 사용자를 추가하고 key를 얻는다.

![사용자 추가](/assets/images/2018-04-11-aws-cli/IAM-add-user.png)

잘 모르겠으면 [공식 문서](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-getting-started.html#cli-quick-configuration)를 참조하자.

발급받은 *AWS Access Key ID*와 *AWS Secret Access Key*는 잘 적어둬야한다. **aws configure**에 필요하다.

```bash
$ aws configure # aws cli 설정정보를 입력하자
AWS Access Key ID [None]: 나의 키 # 방금 발급받은 그 키
AWS Secret Access Key [None]: 내 비밀 키 # 방금 발급받은 그 비밀 키
Default region name [None]: ap-northeast-2 # 서울 리전
Default output format [None]: json
```

이제 aws cli 설정은 다 끝났다.

## 2. 버킷 정책

하지만 여전히 **sync**는 안 될 것이다.

```bash
$ aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website s3://wildrydes-ys-a --region ap-northeast-2 # 복사하려고 했더니
fatal error: An error occurred (AccessDenied) when calling the ListObjects operation: Access Denied # 권한이 없다는 오류가 뜨네
```

버킷 정책을 세워야 한다.

![버킷 정책](/assets/images/2018-04-11-aws-cli/bucket-policy.png)

[정책 생성기](http://awspolicygen.s3.amazonaws.com/policygen.html)를 사용해도 되고, [공식 문서](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/using-iam-policies.html)를 참고해도 된다.

나는 아래와 같이 만들었다.

```javascript
// wildrydes-ys-a 버킷의 모든 리소스에 나만 접근할 수 있게 설정했다.
// 주석은 빼고 입력해야 한다
{
  "Id": "Policy1523420131398",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1523419995280",
      "Action": "s3:*", // S3에 한하여 뭐든지 다 허용
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::s3://wildrydes-ys-a/*", // 대상은 wildrydes-ys-a bucket의 모든 리소스
      "Principal": {
          "AWS": [
              "arn:aws:iam::398594406637:user/ys" // IAM에서 사용자 ARN 값을 넣으면 됨. 요 값은 내 ARM 값
          ]
      }
    }
  ]
}
```

잘못 설정하면 저장이 안된다. 혹시나 잘못 입력할까봐 걱정하며 망설이지 말고 마구 저장 버튼을 눌러보자. **Principal: "*"** 으로 입력하면 public 버킷이 된다.

## 3. 권한 추가

하지만 에러는 여전하다.

```bash
$ aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website s3://wildrydes-ys-a --region ap-northeast-2 # 복사하려고 했더니
fatal error: An error occurred (AccessDenied) when calling the ListObjects operation: Access Denied # 권한이 없다는 오류가 뜨네
```

IAM에서 권한을 주자. 나는 그냥 AdministratorAccess 권한을 주었다.

![권한 추가](/assets/images/2018-04-11-aws-cli/IAM-add-permission.png)

## 4. 끝

이제 잘 된다!

![sync](/assets/images/2018-04-11-aws-cli/final-result.png)