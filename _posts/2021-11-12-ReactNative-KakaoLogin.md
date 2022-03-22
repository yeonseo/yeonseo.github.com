---
title: 리액트 네이티브로 카카오 로그인 구현하기 (2021.11)
author: Yeonseo Nam
date: 2021-11-12 17:30:00 +0900
categories: [ReactNative, Tutorial]
tags: [ReactNative, TIL, Today I Leaned]
comments : true
---

카카오 로그인을 구현하면서, 최근에 발생한 이슈때문에 kotlin debug Error가 발생했었다. 이 문제에 대해서 Readme.md에 수정반영되지 않아서,, 나와 같은 삽질을 줄어들길 바라며!


# React Native 프로젝트 생성

많은 블로그에서 당연하게 TS로 생성을 했는데, JS 환경에서 빌드가 되지 않는다는 이야기를 본 적이 없는 것 같다.

**Tips:** 프로젝트 생성 시, 꼭!! `Typescript` 기반으로 만든다.

# 어려웠던 점

- 라이브러리 종속성 버전 호환문제
- kotlin / javac 버전 맞추기
- maven import



# ts사용하자
## Why : 많은 라이브러리가 ts를 지원한다.

# 오류 발생 시, 우선 적으로 해보면 좋은 대처법
- gradlew clear 실행
cd android
.\gradlew clean


- android 리셋 (in 안드로이드 스튜디오)
File > Invaildate Caches / Restart > 진행

- npm start 다시 구동



===

# 참고 사이트

REST-API 활용한 카카오 소셜 로그인 구현(feat. React) => 삽질 기록기
https://data-jj.tistory.com/53


터미널에서 react-native start 로 로컬 서버를 키고, react-native run-android 로 앱을 구동시키면 정상적으로
https://medium.com/@cute_mustard_sardine_17/react-native-cli-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EA%B0%9C%EB%B0%9C%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%B6%95-%EB%8B%A4%EC%8B%9C-%EC%A0%95%EB%A6%AC-9295a14a2359


카카오 로그인 이미지
웹 환경에서 사용 가능한 카카오 로그인 버튼입니다. 사용자에게 일관된 브랜드 이미지를 전달할 수 있도록 카카오 로그인 버튼 사용법을 참고하여 사용해야 합니다.
https://developers.kakao.com/tool/resource/login


iOS ) Kakao API - 시작하기 (앱키 설명 bb)
https://zeddios.tistory.com/451


How to Migrate a React App to TypeScript
https://kyounghwan01.github.io/blog/TS/React/convert-js-to-ts/#tsconfig-json-%E1%84%8E%E1%85%AE%E1%84%80%E1%85%A1


JS로 작성한 React 프로젝트를 TS로 바꾸며 느낀 점 1
https://guswnl0610.github.io/typescript/jstots/


React 환경 JavaScript → TypeScript (.tx, .tsx 차이)
https://www.kyulabs.app/b8d409a7-1d1a-4a0f-aa04-54e2fcec3692




===

# kakao키해시

https://github.com/react-native-seoul/react-native-kakao-login
https://velog.io/@everydamnday/%EC%B9%B4%EC%B9%B4%EC%98%A4%ED%86%A1-%EB%A1%9C%EA%B7%B8%EC%9D%B8
https://rkdxowhd98.tistory.com/171

## 키해시 구하기
https://machine-woong.tistory.com/420
openssl 설치 후, 환경변수 설정 안 하는 방법

```
keytool -exportcert -alias androiddebugkey -keystore ~./android/app/debug.keystore -storepass android -keypass android | C:\openssl-0.9.8e_X64\bin\openssl sha1 -binary | C:\openssl-0.9.8e_X64\bin\openssl base64
```

===

# Instagram-clone 프로젝트들
https://github.com/Doha26/Instagram-clone


# 참고 서적
리액트 길벗 책
https://github.com/gilbutITbook/080203


===

# KakaoLoginExample 실행하기 
## (react-native-seoul/kakao-login 예제)

1. root 폴더와 example 폴더에 각각
yarn add @react-native-seoul/kakao-login

2. 프로젝트 실행
$ yarn start
$ yarn android

-> unexpexted token ';' 에러가 발생한다면,
```
// KakaoLoginExample\src\utils\i18n\fbt\translatedFbts.json 파일에 아래 내용이 있는지 확인
// 없다면 아래 내용 추가
{"en_US":{},"ko_KR":{}}
```

===

# 카카오 로그인 라이브러리 추가
## ts로 프로젝트 생성

1. @react-native-seoul/kakao-login 추가
yarn add @react-native-seoul/kakao-login


2. 키해쉬 등록
openssl 설치 후, 환경변수 설정 안 하는 방법
keytool -exportcert -alias androiddebugkey -keystore ~./android/app/debug.keystore -storepass android -keypass android | C:\openssl-0.9.8e_X64\bin\openssl sha1 -binary | C:\openssl-0.9.8e_X64\bin\openssl base64

키 해쉬 ex : HR/Fuycr0lC/GsKzu2hut3rs0hI=

url(https://developers.kakao.com/console/app)
해당 앱 > 죄측 메뉴 플랫폼 > 안드로이드 수정 > 위의 키 해쉬 입력



3. Android 수정
\board-rn\android\build.gradle
maven { url 'https://devrepo.kakao.com/nexus/content/groups/public/' }


```
allprojects {
    repositories {
        mavenCentral()
        mavenLocal()
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url("$rootDir/../node_modules/react-native/android")
        }
        +) maven { url 'http://devrepo.kakao.com:8088/nexus/content/groups/public/' }
        maven {
            // Android JSC is installed from npm
            url("$rootDir/../node_modules/jsc-android/dist")
        }

        google()
        maven { url 'https://www.jitpack.io' }
    }
}
```




4. android:allowBackup="true" 수정
board-rn\android\app\src\main\AndroidManifest.xml 에

android:allowBackup="false" => android:allowBackup="true" 수정




5. kotlin version 1.5.0으로 수정
- File > Invaildate Caches / Restart > 진행
- 위 과정 후, @react-native-seoul/kakao-login/android/build.gradle 이 생성됨
- build.gradle(react-native-seoul_kakao-login) 파일 안에,
  => kotlinVersion = '1.3.41' => kotlinVersion = '1.5.0'




6. 프로젝트 내에 앱키 등록
앱키 ex : e1c1ddb1981b89ce59ad076f5b738703

```
// app/src/main/res/values/strings.xml 수정
<resources>
    <string name="app_name">ojtInstaClone</string>
+   <string name="kakao_app_key">e1c1ddb1981b89ce59ad076f5b738703</string>
</resources>
```




(*앱키 앞에 'kakao' 필수*)
```
// AndroidManifest.xml에 추가
     <activity android:name="com.kakao.sdk.auth.AuthCodeHandlerActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />

            <data android:host="oauth"
                android:scheme="kakaoe1c1ddb1981b89ce59ad076f5b738703" />
        </intent-filter>
      </activity>
```




cd android
.\gradlew clean

yarn android











===

# JS에서 TS로 변경한 과정

- 기존 js로 작성한 파일은 유지

- 블로그를 참고하면, @types/xxx로 라이브러리를 설치해야 함 => 코드에서 error가 나는 import를 보며 추가 설치 진행함





===

# 에러난 경우들,,, ^_ㅜ


## 에러를 모두 잡고 npm start를 했지만, 아래와 같은 오류가 발생함
```
 BUNDLE  ./index.js

 ERROR  ReferenceError: Can't find variable: createStackNavigator
 ERROR  Invariant Violation: Module AppRegistry is not a registered callable module (calling runApplication). A frequent cause of the error is that the application entry file path is incorrect.
      This can also happen when the JS bundle is corrupt or there is an early initialization error when loading React Native.
 ERROR  Invariant Violation: Module AppRegistry is not a registered callable module (calling runApplication). A frequent cause of the error is that the application entry file path is incorrect.
      This can also happen when the JS bundle is corrupt or there is an early initialization error when loading React Native.
```

==> import 제대로 했는지 확인



# TypeError: null is not an object (evaluating 'RNKakaoLogins.login')
https://githubmemory.com/repo/react-native-seoul/react-native-kakao-login/issues/108
==> 
https://developers.kakao.com/docs/latest/ko/kakaologin/prerequisite#kakao-login

튜토리얼을 모두 따라 했는데도, 로그인 시도 시 위의 에러가 날 때 => 카카오 로그인 활성화 & 동의항복 설정했는지 확인해보자.

gradlew.bat 파일이 삭제되었는지 확인해보기
(ㅡㅡ 부들부들,,,, 바이러스 프로그램....)
복원이 안될때는 컴퓨터를 껐다 켜기!!