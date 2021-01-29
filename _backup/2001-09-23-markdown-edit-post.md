---
title: md 파일 작성법
author: Yeonseo Nam
date: 2019-08-20 19:34:00 +0900
categories: [etc, Tutorial]
tags: [Markdown, md, TIL, Today I Leaned]
comments : true
---
* Development Env.
* post date : 2019. 09. 21
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)

# 마크다운 문법(syntax)

Files with the .md or .markdown extension

## 제목(Header)

```css
제목 1
======

제목 2
------
```

제목 1
======

제목 2
------

```css
# 제목 1
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6
```
# 제목 1
## 제목 2
### 제목 3
#### 제목 4
##### 제목 5
###### 제목 6



## 강조(Emphasis)

```java
이텔릭체 *별표(asterisks)* 혹은 _언더바(underscore)
볼드체 **별표(asterisks)** 혹은 __언더바(underscore)__
**_이텔릭체_볼드체**
취소선 ~~물결표시(tilde)~~
<u>밑줄</u>
```

이텔릭체 *별표(asterisks)* 혹은 _언더바(underscore)
볼드체 **별표(asterisks)** 혹은 __언더바(underscore)__
**_이텔릭체_볼드체**
취소선 ~~물결표시(tilde)~~
<u>밑줄</u>

### 인라인 코드 강조

```java
`background`혹은 `background-image` 속성으로 요소에 배경 이미지를 삽입할 수 있습니다.
```

## 이미지
상위에 폴더를 하나 만들어서 이미지를 추가하는 방법이 편하다.
- post/
  - images/
    - 파일넣기!

```java
![여기에는 사진 별칭이나 설명](/post/images/2019-09-23-JavaFX-start-image1.png)
Format: ![Alt 여기도 설명설명](url)
```

![ex_screenshot](/post/images/2019-09-23-JavaFX-start-image1.png)

하위 폴더를 하나 만들어서 저장한 이미지를 불러오는 것이 맘 편하다. ㅇㅇ


## 링크

```java
http://github.com - automatic!
[GitHub] (http://github.com)
```

http://github.com - automatic!
[GitHub](http://github.com)

```java
Automatic linking for URLs
Any URL (like http://www.github.com/) will be automatically converted into a clickable link.
```

Automatic linking for URLs
Any URL (like http://www.github.com/) will be automatically converted into a clickable link.

## 코드

```java
```이렇게!
function fancyAlert(arg) {
      if(arg) {
        $.facebox({div:'#foo'})
      }
    }
` `` <-공백 없애고!
```

```java
 function fancyAlert(arg) {
      if(arg) {
        $.facebox({div:'#foo'})
      }
    }
```

## 표
```java
| irst Header                 | Second Header                |
| --------------------------- | ---------------------------- |
| Content from cell 1         | Content from cell 2          |
| Content in the first column | Content in the second column |

or

| irst Header                 | Second Header                |
| --------------------------- | ---------------------------- |
| Content from cell 1         | Content from cell 2          |
| Content in the first column | Content in the second column |

| One        |    Two     |       Three |
| :--------- | :--------: | ----------: |
| cell1      |   cell2    |       cell3 |
| cell111111 | cell222222 | cell3333333 |

```

| First Header                | Second Header                |
| --------------------------- | ---------------------------- |
| Content from cell 1         | Content from cell 2          |
| Content in the first column | Content in the second column |


| irst Header                 | Second Header                |
| --------------------------- | ---------------------------- |
| Content from cell 1         | Content from cell 2          |
| Content in the first column | Content in the second column |

| One        |    Two     |       Three |
| :--------- | :--------: | ----------: |
| cell1      |   cell2    |       cell3 |
| cell111111 | cell222222 | cell3333333 |

## 리스트

```java
### 번호가 없는 경우
* Item 1
* Item 2
  * Item 2a
  * Item 2b

### 번호를 매기는 경우
1. Item 1
1. Item 2
1. Item 3
   1. Item 3a
   1. Item 3b
```
### 번호가 없는 경우
* Item 1
* Item 2
  * Item 2a
  * Item 2b

### 번호를 매기는 경우
1. Item 1
1. Item 2
1. Item 3
   1. Item 3a
   1. Item 3b

### 체크리스트

```java
- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item
```

- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item


##Username @mentions

Typing an @ symbol, followed by a username, will notify that person to come and view the comment. This is called an “@mention”, because you’re mentioning the individual. You can also @mention teams within an organization.

