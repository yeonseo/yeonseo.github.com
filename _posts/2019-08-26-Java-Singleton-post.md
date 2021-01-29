---
title: 싱글톤(SingleTon)
author: Yeonseo Nam
date: 2019-08-26 19:01:00 +0900
categories: [Java, Tutorial]
tags: [SingleTon, class, TIL, Today I Leaned]
comments : true
---
* Development Env.
* post date : 2019. 08. 26
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)

# 싱글톤이란?

바이트 코드가 로드될 때 한 번만 생성되며 생성자가 여러 차례 호출되어도 실제로 하나의 객체를 여러 번 호출하는 것이다. 객채는 하나이고 최초 생성 이후에 호출된 생성자에 저장된 값들을 부르게 된다. 이와 같은 디자인 유형을 싱글턴 패턴이라고 한다.


## 왜 사용하는건가요?

하나의 객체만을 생성하고 getInstance 메서드를 통해서만 값을 return 받는다. 모든 사용자에게 동일한 인스턴스를 반환하는 작업을 수행하기 때문에 일률적인 작업에 사용된다고 한다.


### 간단한 싱글톤 예제

#### SingleTon Class

```java
class SingleTon {
	//private 접근 제어자를 통해서 다른 클래스에서 접근하지 못하게 함
	private String string = "과제하자.....";
	private static SingleTon single; 
    
    //생성자와 초기화 방법을 외부에서 접근하지 못 하도록 모두 막음
    private SingleTon() {
	}
	static{
		single = new SingleTon();
	}
	
    //클래스명으로도 접근하지 못하므로 특정메소드와 getters, setters로 접근할 수 있게 함
	public static SingleTon getSingleTon() {
		if(single==null) single = new SingleTon();
		return single;
	}
	public void print() {
		System.out.println("공부할 시간이야.. :-) "+string);
	}
	public String getString() {
		return string;}
	public void setString(String string) {
		this.string = string;}
}
```

위의 코드에서 static으로 선언된 부분을 먼저 보면, SingleTon의 객체 참조 변수를 먼저 만든 것을 볼 수 있다. `private static SingleTon single = new SingleTon();` 이렇게 바로 객체를 만들어 참조 변수에 대입하는 방법도 있지만, 아래의 두 가지 방법을 살펴보겠다. 

```java
    ...
    private static SingleTon single;
    ...
    /*1. 블럭 초기화 */
    static{
        single = new SingleTon();
    }
    ...
    /*2. 메소드를 통해 초기화 */
    public static SingleTon getSingleton() {
        if(single==null) single = new SingleTon();
        return single;
    }
    ...
```

두 가지 방법 모두 앞에 static 제어자를 붙여 로드될 때 객체가 만들어지도록 했다. 첫 번째에선 블럭 초기화를 이용해 SingleTon 객체를 만들어 주었다. 그 다음 getSingleton 메소드를 보면 `if(single==null) single = new SingleTon();` 즉, 객체가 만들어 지지 않은 default 상태라면 객체를 만든다. public 접근 제어자로 선언되었기 때문에 다른 클래스에서도 접근 할 수 있고 값을 retrun 받을 수 있다.

# 
#### SingleTon Test Class

아래는 SingleTon 클래스를 구동해 본 예제이다.


```java
public class SingleTonTest {
	public static void main(String[] args) {
		SingleTon single = SingleTon.gerSingleton();
		single.print(); //공부할 시간이야.. :-) 과제하자.....
		single.setString("nonono......");
		single.print(); //공부할 시간이야.. :-) nonono......
	}
}
```
객체를 생성할 수 없기 때문에 `SingleTon.gerSingleton()`를 통해서 객체 참조 변수에 SingleTon 객체의 번지값을 받았고, print, getString, setString 메소드를 통해서 간접접근이 허용되므로 setString 메소드를 통해 `string`으로 간접 접근하는 것을 볼 수 있다.


다시 코드를 작성하는데 있어서 특징을 정리하자면,
* 접근제어자는 private
* 제어자는 static
* defaul 생성자도 private로 선언해줌으로써 객체 생성은 외부에서 하지 못하도록!!

