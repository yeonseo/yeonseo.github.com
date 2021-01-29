---
title: AWS 환경설정 (django 배포해보기)
author: Yeonseo Nam
date: 2020-06-16 19:34:00 +0900
categories: [Django, Angular, Tutorial]
tags: [Python, Django, Web, Backend, AWS, TIL, Today I Leaned]
comments : true
---


이제까지 localhost에서만 프로젝트를 진행했습니다. 하지만 다른사람이 볼 수 없죠. 그래서 '배포'하는 과정을 통해서 다른 사람들이 나의 사이트에 들어올 수 있게 작업을 해야합니다. 이번 포스팅에서는 Django 프로젝트를 배포하는 과정을 담았습니다.
배포하기 위해서는 누구나 들어올 수 있도록 만들어진 PC에 프로젝트를 심어주면 됩니다. 또 하나의 PC를 만들어야 하는데, 보통 리눅스os를 사용해서 서버를 만듭니다. PC환경은 (특히나 리눅스os) 알면 쉽고 모르면 한없이 어려워서 세팅하는 과정은 너무 시간 소모가 많은 작업이죠. yum install 이니, apt-get install 같은 것들은 또 뭔가 싶기도 하고요.

AWS의 Elastic Beanstalk이라는 서비스는 기본적으로 PC환경을 만들어줍니다. 자동차로 예를 들어본다면 아무 옵션없는 깡통 상태를 만들어준다고 볼 수 있습니다. 깡통에 django, django-rest 등등을 설치부터 하면 됩니다! PC 환경설정에 친숙하지 않다면 이것 역시 쉽지는 않게 다가 올테지만, 조금씩 하다보면 됩니다. 🧐

본 포스팅은 [https://academy.nomadcoders.co/](https://academy.nomadcoders.co/)의 "[풀스택] 에어비엔비 클론 코딩" 을 기초로 하여 개인 프로젝트를 진행한 과정을 기초로 작성되었습니다. 더 자세한 설명이 궁금하신 분은 노마드 코더의 강의를 들어보세요! 많은 도움을 받으실겁니다 :-)

<br/><br/><br/><br/><br/>

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

여기까지 Elastic Beanstalk 생성을 하였고, 이어서 Elastic Beanstalk 환경설정을 하겠습니다. Elastic Beanstalk에 본격적으로 환경설정을 해 봅니다. 아무 옵션없는 깡통 상태에서 필요한 옵션을 추가합니다. 추가 주문을 하는 방법은 requirements.txt 라는 주문서로 Elastic Beanstalk에게 알려주면 됩니다. 그러면 Elastic Beanstalk은 주문서대로 설치를 해서 깡통에 업그레이드를 진행합니다.

요약하자면 3가지 작업을 진행합니다.
* requirements.txt 작성, Elastic Beanstalk 환경 구축
* RDS 생성 및 설정 (RDS : aws의 DB서비스)
* Elastic Beanstalk과 RDS를 연결


# AWS CLI Setting 2

## 1. requirements.txt 생성

### requirements.txt 만드는 방법
```
> pipenv run pipenv_to_requirements -f

or

> pipenv freeze > requirements.txt
```
위 과정은 지금까지 프로젝트를 진행하면서 pip install로 설치한 모든 패키지들의 정보가 requirements.txt로 만들어집니다. 이 requirements.txt라는 주문서를 가지고 aws Elastic Beanstalk가 스스로 설치합니다. 예를 들어, 

```python
# requirements.txt
django-phone-field==1.8.1
django-rest-auth==0.9.5
django-seed==0.2.2
django==3.0.7
```

🔺 위와 같은 requirements.txt 내용을 아래와 같이 실행합니다. 🔻

```
yum install django-phone-field==1.8.1
yum install django-rest-auth==0.9.5
yum install django-seed==0.2.2
yum install django==3.0.7
```
yum이 뭔지는 알 필요 없습니다. pip install django==3.0.7처럼 패키지를 설치하는 과정이 requirements.txt에 있는 내용들로 진행되는구나 하고 이해를 해주세요~

<p></p>
<p></p>
<p></p>

## 2. DB Setting
DB는 이제껏 local로 운영하던 것에서 원격으로 둡니다. 저는 강좌를 따라 aws에서 제공하는 DB서비스 중에서 PostgreSQL을 사용했습니다. Elastic Beanstalk 안에 DB를 두지 않고 따로 만드는 이유는 크게 다음과 같습니다.

1. Elastic Beanstalk이 삭제되면 그 안의 DB를 손실하게 됩니다.
2. 구서버에서 신서버로 운영을 변경할 시, DB 연결 정보만으로 이전의 데이터를 이전할 수 있습니다.
3. 서버와 DB를 함께 두는 것보다 따로 관리하는 것이 보안상 더 좋습니다.

<p></p>
<p></p>

### (1) settings.py에서 sqlite3 / postgresql 설정
DEBUG 중인지 아닌지에 따라 DB를 달리하기 위해서 변수를 추가합니다.

``` python
# .env
DEBUG=Ture
```

DEBUG의 값을 읽어, 디버그가 아닌 상태에서는 PostgreSQL을 사용하도록 합니다.
```python
# settings.py

DEBUG = bool(os.environ.get("DEBUG"))

...

if DEBUG:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
        }
    }
else:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql",
            "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
        }
    }
```
<p></p>
<p></p>
<p></p>

### (2) aws에서 RDS 생성
aws에서 제공하는 DB서비스를 통해 데이터베이스를 생성합니다.
[https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2#](https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2#)

좌측 사이드바, 데이터 베이스 -> 데이터 베이스 생성
* Postgresql 선택
* free 선택
* username / password 입력
* 나머지는 건들지 말고 생성!

#### RDS란?
Amazon Relational Database Service (Amazon RDS) makes it easy to set up, operate, and scale a relational database in the cloud. It provides cost-efficient and resizable capacity while automating time-consuming administration tasks such as hardware provisioning, database setup, patching and backups.

Amazon Relational Database Service (Amazon RDS)는 관계형 데이터베이스의 설정, 운영 및 확장을 쉽게 만들어 줍니다. 하드웨어 프로비저닝, 데이터베이스 설정, 매치 및 백업과 같은 시간소모적 관리 작업을 자동화 해보세요! 효율적인 가격과 데이터베이스 용량을 조정할 수 있습니다. :-)

프로비저닝(Provisioning) : 네트워크가 사용자에게 새로운 서비스를 제공할 수 있도록 네트워크를 준비하고 설치하는 과정. 서버 프로비저닝은 적절한 시스템, 데이터 및 소프트웨어를 사용하여 서버를 준비하고 네트워크 작업을 위해 서버를 준비하는 작업 세트입니다. 
> 출처: https://bfre.tistory.com/entry/IT-용어-풀이-프로비저닝Provisioning [IT한량]

<p></p>
<p></p>
<p></p>

### (3) requirements.txt 에 Postgresql 추가

여기서 시작되는 <strong>'Postgresql setting'</striong> 과정에서 굉장히 힘들었습니다. 그리고 강의 댓글에서도 오류가 난다는 글들이 많이 보였구요! 주의를 요합니다 :-) 그럼 시작해봅시다!

```python
# requirements.txt
psycopg2==2.8.4 (추가)
-> psycopg2-binary==2.8.4 (저는 설치 오류가 나서, binary로 설치했습니다.)
```

📌psycopg2-binary==2.8.4는 직접 작성하여 저장해줍니다! 직접 작성한 내용이므로 freeze, pipenv_to_requirement는 하지 않습니다. pip install 되지 않은 패키지이므로 freeze, pipenv_to_requirement 작업을 진행하면 직접 작성한 psycopg2-binary는 지워집니다.

<p></p>
<p></p>
<p></p>

### (4) settings.py에 Postgresql 정보 입력

생성된 데이터베이스 정보를 https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2#database 에서 확인하고 settings.py에 입력을 해 줍니다.
DEBUG값에 따라 바뀌도록 다음과 같이 입력해줍니다.

RDS 정보는 os.environ.get를 통해 os 환경변수에서 가져올 수 있도록 설정합니다.

```python
if DEBUG:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.sqlite3",
            "NAME": os.path.join(BASE_DIR, "db.sqlite3"),
        }
    }
else:

    DATABASES = {
        "default": {
            "ENGINE": "django.db.backends.postgresql",
            "HOST": os.environ.get("RDS_HOST"),
            "NAME": os.environ.get("RDS_NAME"),
            "USER": os.environ.get("RDS_USER"),
            "PASSWORD": os.environ.get("RDS_PASSWORD"),
            "PORT": "5432",
        }
    }
```

<p></p>
<p></p>
<p></p>

### (4) os 환경변수에 Postgresql 정보 입력

[https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk/home?region=ap-northeast-2#/welcome](https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk/home?region=ap-northeast-2#/welcome)


1. Elastic Beanstalk -> applocations 클릭
2. 어플리케이션 이름 클릭 -> 환경이름 클릭
3. 좌측 사이드바, 구성 클릭 -> 소프트웨어, 편집 클릭
4. 제일 아래 환경변수 추가
    ``` python
    RDS_HOST="your host" <- RDS에서 생성된 endpoint
    RDS_NAME="your name" <- RDS에서 비밀번호
    RDS_USER="your user" <- RDS에서 비밀번호
    RDS_PASSWORD="your pass" <- RDS만들 때 적은 비밀번호
    ```
![ex_screenshot](/post/images/2020-06-17_100043.png)

작성하고 저장하면, DB가 적용 중이라고 표시됩니다. 다 완료가 되면, 

1. requirements 등의 변경사항을 커밋
2. eb deploy
3. deploy가 끝나면 eb logs를 통해 서버의 상태를 살펴볼 수 있는데, 패키지도 설치되었고 여러가지 바빴다는 것이 보인다. 오.
4. 무사히 끝났다면 다행, 혹시나 에러가 났다면,,,  ^_ㅠ 아래를 참고해보시길!

<p></p>
<p></p>
<p></p>

### (5) packages.config 만들어주기

.ebextensions 폴더 안에 packages.config 파일을 만들어서 리눅스가 가지고 있는 프로그램을 이용할 수 있다.
yum을 이용해서 postgresql96(PostgreSQL 9.6버전)을 적어주었다. 버전은 자신이 만든 RDS의 구성을 확인하여 설치하자

 - .ebextensions
   - packages.config

```
packages:
  yum:
    postgresql96-devel: []
```

<p></p>
<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

### Elastic Beanstalk, Postgresql 설치 <string style="color:red">Fail</string>,,,

Postgresql 설치 방법에는 크게 2가지가 있습니다. 그리고 두 방법에는 다음과 같은 이유 때문에 에러가 발생할 수 있습니다.

1. requirements.txt에 패키지 적어주기

- yum 또는 apt-get이 psycopg2==2.8.4 또는  psycopg2-binary==2.8.4 을 지원하지 않음
===> <string style="color:red">Fail</string>

이때 나타나는 ERROR,
```
2020/06/19 03:08:01.699877 [ERROR] An error occurred during execution of command [app-deploy] - [InstallDependency]. Stop running the command. Error: fail to install dependenc
ies with requirements.txt file with error Command /bin/sh -c python3 -m pipenv install -r requirements.txt --skip-lock failed with error exit status 1. Stderr:Requirements fil
e provided! Importing into Pipfile…
An error occurred while installing psycopg2==2.8.4! Will try again.
```
'??? : 니가 준 requirements.txt대로 설치를 시도 해봤어. 헤이헤이,, 근데 문제가 있어,, 나 psycopg2==2.8.4 얘 때문에 오류야! 다르게 시도 해봐봐,,'


2. .ebextensions 폴더 안에 packages.config 만들어주기

```python
# ex packages.config
commands:
  01_install_rhel_pg:
    command: "(yum repolist |grep -q pgdg96) || sudo yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-6-x86_64/pgdg-centos96-9.6-3.noarch.rpm -y"
  02_install_pg_devel:
    command: "sudo yum install postgresql96-devel -y"
```
- sudo yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-6-x86_64/pgdg-centos96-9.6-3.noarch.rpm
- 위의 커멘드를 실행하는 과정에서 다운로드 받을 패키지의 파일명이 달라지거나 사라져서 404 에러가 발생
===> <string style="color:red">Fail</string>

3. (+) 그 후, 커맨드를 바꿔 commit -> deploy해도 변경된 내용이 적용이 되지 않아 문제가 지속된다는 반응도 많이 봤습니다. 그럴 땐 환경(EB, Elastic Beanstalk)을 삭제한 후, 다시 시도하면 해결됬다는 분들도 계셨습니다. 

<p></p>

저는 requirements.txt에서 psycopg2-binary==2.8.4로 바꿔 준 후, EB 환경을 삭제하고 새로 만들어서 eb deploy를 해 주었습니다. packages.config에서 직접 설치하는 커멘드도 써봤었는데, 문제가 많은 부분이 있더라구요. 

<p></p>
<p></p>
<p></p>

무사히 환경설정을 마치셨다면 축하드립니다. :-) 다음으로는 호스팅의 접근권한 설정을 진행하겠습니다.


# AWS CLI Setting 1

[https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2#SecurityGroups:search=sg-c4ac9aa1](https://ap-northeast-2.console.aws.amazon.com/ec2/v2/home?region=ap-northeast-2#SecurityGroups:search=sg-c4ac9aa1)

## aloow host 바꿔주기

``` python
# settings.py
# ALLOWED_HOSTS = []
ALLOWED_HOSTS = [".elasticbeanstalk.com"]
```

이렇게 elasticbeanstalk에서 접근하면, 웹페이지를 보여주도록 허용을 바꿔줍니다. :-)