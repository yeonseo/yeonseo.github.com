---
layout: post
title: AWS 환경설정 1
description: "Django & angular를 배포해보자!"
tags: [Python, Django, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Django, Django&Angualr]
---


이제까지 localhost에서만 프로젝트를 진행했습니다. 하지만 다른사람이 볼 수 없죠. 그래서 '배포'하는 과정을 통해서 다른 사람들이 나의 사이트에 들어올 수 있게 작업을 해야합니다. 이번 포스팅에서는 Django 프로젝트를 배포하는 과정을 담았습니다.
배포하기 위해서는 누구나 들어올 수 있도록 만들어진 PC에 프로젝트를 심어주면 됩니다. 또 하나의 PC를 만들어야 하는데, 보통 리눅스os를 사용해서 서버를 만듭니다. PC환경은 (특히나 리눅스os) 알면 쉽고 모르면 한없이 어려워서 세팅하는 과정은 너무 시간 소모가 많은 작업이죠. yum install 이니, apt-get install 같은 것들은 또 뭔가 싶기도 하고요.

AWS의 Elastic Beanstalk이라는 서비스는 기본적으로 PC환경을 만들어줍니다. 자동차로 예를 들어본다면 아무 옵션없는 깡통 상태를 만들어준다고 볼 수 있습니다. 깡통에 django, django-rest 등등을 설치부터 하면 됩니다! PC 환경설정에 친숙하지 않다면 이것 역시 쉽지는 않게 다가 올테지만, 조금씩 하다보면 됩니다. 🧐

본 포스팅은 [https://academy.nomadcoders.co/](https://academy.nomadcoders.co/)의 "[풀스택] 에어비엔비 클론 코딩" 을 기초로 하여 개인 프로젝트를 진행한 과정을 기초로 작성되었습니다. 더 자세한 설명이 궁금하신 분은 노마드 코더의 강의를 들어보세요! 많은 도움을 받으실겁니다 :-)

<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

---
* Development Env.
* post date : 2020. 06. 17
* OS : macOS Catalina 64bit, Windows 10 64bit
* Python : 3.8
* Django : 2.2.5
* Tool : PyCharm 2018.3.5 (Community Edition)
* JRE : 1.8.0_152-release-1343-b28 amd64
* JVM : OpenJDK 64-Bit Server VM by 
---

# AWS CLI Setting 1

## 1. EB CLI 설치 및 AWS Elastic Beanstalk 환경 생성

### (1) EB CLI 설치

```
> pipenv install awsebcli --dev 
```

#### Windows에 Python, pip 및 EB CLI 설치
[https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install-windows.html](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install-windows.html)

<p></p>
<p></p>
<p></p>

### (2). AWS Elastic init - 1

```
> eb init
...
(default is 3): 10 // 서울로 셋
(잠시 대기하고 아래의 3. AWS IAM user 생성 과정을 진행)
```
<p></p>
<p></p>
<p></p>

### (3). AWS IAM user 생성
IAM(https://aws.amazon.com/ko/iam/?nc2=type_a)으로 가서 
-> AWS 계정 생성
[https://console.aws.amazon.com/iam/home#/users](https://console.aws.amazon.com/iam/home#/users)
사용자 -> 사용자 추가

* 사용자 이름 입력
* AWS 액세스 유형 : 프로그래밍 방식 액세스 (체크)

다음

권한설정
* 기존 정책 직접 연결
* AdministratorAccess

다음
Tag - 넘어감
다음
검토 - 넘어감

생성 완료! 
다시 볼 수 없으니까, download 받아 둘 것!

ex 액세스 키 : AKIAU7HXRP56YQCUFR5O
ex 비밀 액세스 키 : qopAyecDDjAgNibGP7A7b3PveQwgNA61vZ/cZCL6

<p></p>
<p></p>
<p></p>

### (4). AWS Elastic init - 2
아까 대기중이던 콘솔 창에 이어서 입력 진행함

```
액세스 키 : AKIAU7HXRP56YQCUFR5O
비밀 액세스 키 : qopAyecDDjAgNibGP7A7b3PveQwgNA61vZ/cZCL6
디폴트 이름은 "입력한 이름이 뜸" 니? ㅇㅇ
너 파이썬 쓰니? ㅇㅇ
버전 선택ㄱㄱ : pipfile에 적힌 버전을 선택
코드 커밋할래? ㄴㄴ
ssh 설정? ㅇㅇ,, or ㄴㄴ,,
Done!!
```
<p></p>
<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

## 2. Django 애플리케이션을 Elastic Beanstalk에 배포

#### Django 애플리케이션을 Elastic Beanstalk에 배포
[https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create-deploy-python-django.html](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create-deploy-python-django.html)

<p></p>
<p></p>
<p></p>

### (1).  .ebextensions 생성
장고 프로젝트 폴더 안에, .ebextensions폴더 생성

> .ebextensions 디렉터리 내에서 다음 텍스트가 있는 django.config라는 구성 파일을 추가합니다.
> 예 ~/ebdjango/.ebextensions/django.config


```
option_settings:
  aws:elasticbeanstalk:container:python:
    //WSGIPath: ebdjango/wsgi.py ->
    WSGIPath: config/wsgi.py
```
<strong>✨엘라스틱 빈은 git commit을 가져감. hoxy,, 파일을 바꿨다면 꼭  commit 후에 eb create,  eb depoly 할 것!!!!</strong>
> 환경을 생성하고 Django 애플리케이션을 배포하려면
이 다음부터 진행함. init는 위에서 진행했으므로 다음 2.번 부터 진행한다.

2. (선택 사항) SSH를 통해 애플리케이션을 실행하는 EC2 인스턴스에 연결할 수 있도록 eb init를 다시 ... -> 건너뜀

3. 환경을 만들고 eb create로 해당 환경에 애플리케이션을 배포합니다.
```
// ~/ebdjango$ eb create django-env
> eb create (각자 프로젝트명)
```
위 작업이 끝나면 서버가 작업중인 것을 확인할 수 있음
[https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk](https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk)

ec2에 가서 인스턴스 생성 확인하기
[https://us-east-2.console.aws.amazon.com/ec2/v2/home?region=us-east-2#Home:](https://us-east-2.console.aws.amazon.com/ec2/v2/home?region=us-east-2#Home:)


6. 파일을 저장한 후 eb deploy를 실행해 애플리케이션을 배포합니다. eb deploy를 실행하면 EB CLI가 프로젝트 디렉터리의 콘텐츠를 번들링한 후 이를 환경에 배포합니다.

#### eb deploy 란?
설명
초기화된 프로젝트 디렉터리에서 실행 중인 애플리케이션으로 애플리케이션 소스 번들을 배포합니다.

git이 설치되어 있는 경우 EB CLI에서는 git archive 명령을 사용하여 최신 .zip 명령의 내용에서 git commit 파일을 생성합니다.

하지만 프로젝트 디렉터리에 .ebignore가 있으면 EB CLI가 소스 번들을 생성하기 위해 git 명령과 구문을 사용하지 않습니다. 즉 EB CLI는 .ebignore에 지정된 파일을 무시하고 다른 모든 파일을 포함시킵니다. 특히 커밋되지 않은 소스 파일을 포함시킵니다.
[https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb3-deploy.html](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb3-deploy.html)

<p></p>
<p></p>
<p></p>

# todo
위의 서버 개설 작업을 test / production 두가지 버전으로 만든다

<p></p>
<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

여기까지 Elastic Beanstalk 생성을 하였고, 다음 포스팅에서 이어서 Elastic Beanstalk 환경설정을 하겠습니다.