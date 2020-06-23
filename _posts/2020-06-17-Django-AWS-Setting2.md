---
layout: post
title: AWS 환경설정 2
description: "Django & angular를 배포해보자!"
tags: [Python, Django, Web, Backend,TIL, Today I Leaned]
comments : true
category : [Django, DjangoAngualr]
---


저번 포스팅에 이어서 이번에는 Elastic Beanstalk에 본격적으로 환경설정을 해 봅니다. 아무 옵션없는 깡통 상태에서 필요한 옵션을 추가합니다. 추가 주문을 하는 방법은 requirements.txt 라는 주문서로 Elastic Beanstalk에게 알려주면 됩니다. 그러면 Elastic Beanstalk은 주문서대로 설치를 해서 깡통에 업그레이드를  진행합니다.

이번 포스팅에서는 3가지 작업을 진행합니다.
* requirements.txt 작성, Elastic Beanstalk 환경 구축
* RDS 생성 및 설정 (RDS : aws의 DB서비스)
* Elastic Beanstalk과 RDS를 연결

본 포스팅은 https://academy.nomadcoders.co/[Nomad Coders] 의 "[풀스택] 에어비엔비 클론 코딩" 을 기초로 하여 개인 프로젝트를 진행한 과정을 기초로 작성되었습니다. 더 자세한 설명이 궁금하신 분은 노마드 코더의 강의를 들어보세요! 많은 도움을 받으실겁니다 :-)

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

<p></p>
<p></p>
<p></p>
<p></p>
<p></p>

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
https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2#

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

https://ap-northeast-2.console.aws.amazon.com/elasticbeanstalk/home?region=ap-northeast-2#/welcome

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

무사히 환경설정을 마치셨다면 축하드립니다. :-) 다음 포스팅에서는 호스팅의 접근권한 설정을 진행하겠습니다.