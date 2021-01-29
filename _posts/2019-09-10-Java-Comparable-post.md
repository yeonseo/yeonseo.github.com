---
title: comparable 상속받아 비교하기
author: Yeonseo Nam
date: 2019-09-10 19:34:00 +0900
categories: [Java, Tutorial]
tags: [comparable, compareTo, Algorism, code, TIL, Today I Leaned]
comments : true
---

* Development Env.
* post date : 2019. 09. 10
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)

## Problem

* 객체 생성해서 비교하기
* Comparable 상속받아 compareTo 구현하기
* instanceof 사용해서 적절한 객체만 받아 비교하기
* 여기서는 사각형의 넓이 값을 비교하는 것으로 구현하기

### Comparable Class

```java
public interface Comparable {
    public default int comparaTo(Object object) {
        return 0;
    }
}
```

### Rectangle Class (비교할 객체)

```java
public class Rectangle implements Comparable{
    public int width;
    public int height;
    
    public Rectangle(int width, int height) {
        this.width=width;
        this.height=height;
    }
    @Override
    public String toString() {
        return "Rectangle [width=" + width + ", height=" + height + "]";
    }
    public int getArea() {
        return this.width*this.height;
    }
    
    public int comparaTo(Object other) {
        Rectangle otherArea;
        int returnValue=0;
        if(other instanceof Rectangle) {  //other가 Rectangle라면, 비교시작
            otherArea = (Rectangle)other;
            
            /*Rectangle의 getArea()값 비교*/
            if(this.getArea() == otherArea.getArea()) {
                System.out.println("same");
                returnValue= 0;
            }
            else if (this.getArea() < otherArea.getArea()) {
                System.out.println("small");
                returnValue= -1;
            }
            else {
                System.out.println("big");
                returnValue= 1;
            }
        }
        else {  //other가 Rectangle아니라면, 비교하지 않고 종료
            System.out.println("네가 아니야..... Rectangle.... ");
            System.exit(0);
        }
        return returnValue;
    }
    
}
```

### RectangleTest Class (객체를 생성, 사용할 객체)

```java
public class RectangleTest {
    public static void main(String[] args) {
        Rectangle re1 = new Rectangle(10,20);
        Rectangle re2 = new Rectangle(20,20);
        NoRectangle noRe1=new NoRectangle(10,20);
        
        re1.toString();
        re2.toString();
        
        re1.comparaTo(re2);
        re2.comparaTo(re1);
        re1.comparaTo(noRe1);
    }
}
```