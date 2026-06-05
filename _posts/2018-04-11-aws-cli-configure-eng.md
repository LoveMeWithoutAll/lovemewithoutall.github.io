---
layout: single
title: Initial Setup for the AWS CLI
date: 2018-04-11 13:07:30.000000000 +09:00
header:
  teaser: "https://d1.awsstatic.com/Test%20Images/Kate%20Test%20Images/Serverless_Web_App_LP_assets-01.998a9201f50434767e58269c5b71b485014a4531.png"
  image: "https://d1.awsstatic.com/Test%20Images/Kate%20Test%20Images/Serverless_Web_App_LP_assets-01.998a9201f50434767e58269c5b71b485014a4531.png"
type: post
categories:
- IT
tags: [AWS, aws-cli, IAM]
---

I'm following AWS's [Build a Serverless Web Application](https://aws.amazon.com/ko/serverless/build-a-web-app/) tutorial. It's been three years since I last touched AWS hands-on. In the meantime, it has no doubt changed a lot. Even configuring the AWS CLI was a struggle. To follow the tutorial, I put together the steps for setting up the AWS CLI so it can work with S3.

## 1. aws configure

After installing the AWS CLI as the tutorial instructed, I casually ran the first command: **aws sync**. As you can see below, it neatly throws an error.

```bash
$ aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website s3://wildrydes-ys-a --region ap-northeast-2 # I tried to copy it
fatal error: Unable to locate credentials # but it says the credentials can't be found
```

For the AWS CLI to access my S3 bucket, it needs login credentials. Let's set them up. First, add a user in IAM and obtain a key.

![Add user](/assets/images/2018-04-11-aws-cli/IAM-add-user.png)

If you're not sure how, refer to the [official documentation](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-getting-started.html#cli-quick-configuration).

Make sure to write down the *AWS Access Key ID* and *AWS Secret Access Key* you receive. You'll need them for **aws configure**.

```bash
$ aws configure # let's enter the AWS CLI configuration
AWS Access Key ID [None]: my key # the key I just received
AWS Secret Access Key [None]: my secret key # the secret key I just received
Default region name [None]: ap-northeast-2 # Seoul region
Default output format [None]: json
```

Now the AWS CLI setup is all done.

## 2. Bucket Policy

But **sync** still won't work.

```bash
$ aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website s3://wildrydes-ys-a --region ap-northeast-2 # I tried to copy it
fatal error: An error occurred (AccessDenied) when calling the ListObjects operation: Access Denied # but it says I don't have permission
```

You need to set up a bucket policy.

![Bucket policy](/assets/images/2018-04-11-aws-cli/bucket-policy.png)

You can use the [policy generator](http://awspolicygen.s3.amazonaws.com/policygen.html), or refer to the [official documentation](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/using-iam-policies.html).

I created mine as follows.

```javascript
// I set it up so that only I can access all resources in the wildrydes-ys-a bucket.
// Remove the comments before entering it
{
  "Id": "Policy1523420131398",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "Stmt1523419995280",
      "Action": "s3:*", // allow anything, limited to S3
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::s3://wildrydes-ys-a/*", // the target is all resources in the wildrydes-ys-a bucket
      "Principal": {
          "AWS": [
              "arn:aws:iam::398594406637:user/ys" // put the user ARN value from IAM here. This value is my ARN
          ]
      }
    }
  ]
}
```

If you set it up incorrectly, it won't save. Don't hesitate out of worry that you might enter something wrong; just go ahead and mash that save button. If you enter **Principal: "*"**, it becomes a public bucket.

## 3. Adding Permissions

But the error still persists.

```bash
$ aws s3 sync s3://wildrydes-us-east-1/WebApplication/1_StaticWebHosting/website s3://wildrydes-ys-a --region ap-northeast-2 # I tried to copy it
fatal error: An error occurred (AccessDenied) when calling the ListObjects operation: Access Denied # but it says I don't have permission
```

Let's grant permissions in IAM. I just gave it AdministratorAccess permission.

![Add permission](/assets/images/2018-04-11-aws-cli/IAM-add-permission.png)

## 4. Done

Now it works!

![sync](/assets/images/2018-04-11-aws-cli/final-result.png)
