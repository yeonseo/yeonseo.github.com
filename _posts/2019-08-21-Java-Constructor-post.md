---
title: 생성자 함수의 오버로드
author: Yeonseo Nam
date: 2019-08-21 19:13:00 +0900
categories: [Java, Tutorial]
tags: [constructor, overload, TIL, Today I Leaned]
comments : true
---
* Development Env.
* post date : 2019. 08. 21
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)

# 생성자 함수와 오버로드.. 오버로드?
<div><b>"생성자 함수의 오버로드"</b>를 이야기 하면서 <b>"오버로드"</b>라는 것이 무엇인지 슬쩍 던져보겠다. Java는 <b>같은 함수명으로 여러 번 정의하는 것</b>을 허용하고 우리는 그것을 편리하게 사용할 수 있다. 예를 들어, 하나의 <b>a()</b>함수명으로 a(<b>여</b>), a(<b>러</b>), a(<b>가</b>), a(<b>지</b>)의 작업을 할 수 있는 것을 오버로드라고 한다. 같은 함수명으로 여러개를 사용한다는 것이 무슨 의미이고 왜 오버로드가 필요한지 아래의 예시 코드에 나타난 생성자함수로 살펴보겠다.</div>

```java
public class NumberBox {
	private  int iValue; private float fValue;
	private double dValue; private char cValue;

	public NumberBox() {
		this.iValue = 0; this.fValue = 0.0f; this.dValue = 0.0; this.cValue = 65;
	}
	public NumberBox(int iValue, float fValue,
			double dValue) {
		this.iValue = iValue; this.fValue = fValue;
		this.dValue = dValue; this.cValue = 65;
	}
	public NumberBox(int iValue, float fValue,
			double dValue, char cValue) {
		this.iValue = iValue; this.fValue = fValue;
		this.dValue = dValue; this.cValue = cValue;
	}
		...
```
<div>모두 NumberBox로 함수명이 동일하지만 각각의 생성자 함수가 하는 일은 다르다. 첫 번째는 초기값을 모두 default(0으로!) 값으로 초기화한다. 두 번째는 iValue와 fValue, dValue만 매개변수로 초기값을 명시해주고, 세 번째엔 모든 인스턴스 변수값을 매개변수로 들어온 값으로 초기화한다. 3개의 생성자 함수를 만든 클래스를 NumberBoxTest클래스에서 객채화해보겠다.
</div>

```java
public class NumberBoxTest {

	public static void main(String[] args) {
		NumberBox box1 = new NumberBox();
		NumberBox box2 = new NumberBox(10,10.258f,10.25);
		NumberBox box3 = new NumberBox(20,20.0f,20.0,'c');
        	...
```
<div> NumberBox 참조변수들(box1, box2, box3)은 각각 NumberBox 클래스의 첫 번째, 두 번째, 세 번째 생성자 함수를 사용하여 객체를 생성했다. 같은 함수명이름으로 매개변수만 다르게 입력하여 객체를 초기화한 것이다.(wow)

만약 Java가 오버로딩을 허용하지 않았다고 생각해본다면, 생성자함수명을 모두 달리 해야할 것이고, 사용되는 매개변수에 따라 모두 다른 이름으로 함수명을 작명해야 할 것이다. 각각의 함수명은 직관적으로 알 수 있도록 센스있게 작명해야하는데, ... 생각만해도 벌써 싫다. 하지만.. 이게 이렇게 유용할 일인가 싶을 수도 있다. 그럼 다음 경우를 생각해보자.(유용하다고 생각된다면 제일 아래 "생성자함수 오버로딩 작성법"으로 가서 시간을 아끼자.)


<b>여러가지의 덧셈</b>을 한다고 생각해보면 어떨까 싶다. 덧셈 함수를 만드는데 4가지의 함수가 필요하다고 가정해보겠다.

* 오버로딩이 허용되지 않는 상황
* 4개의 함수는 모두 같은 기능을 하지만 모두 다른 함수명을 사용

위와 같은 상황을 생각해보자는 것이다. 물론 덧셈은 간단한 코딩으로 결과물을 얻어낼 수 있지만, 오버로딩의 간편함이 얼마나 좋은지를 조금 느끼고 앞으로 프로그래밍하는데 있어서 함수의 오버로딩이 가능한 상황이라면 적극 활용했으면 좋겠다. 위의 2가지 제약을 숙지했다면 아래의 경우들의 함수명을 지어보자.
</div>

* 2개의 정수 합 :
* 개의 실수 합 :
* 3개의 정수 함 :
* 3개의 실수와 1개의 정수 합 :


<div>
혹시 각각의 경우에 적절한 함수명을 생각할 수 있었는가? 만약 자신있게 ok를 외쳤다면,
다음의 경우에는 어떻게 직관적이고 효율적으로 사용하기 좋은 함수명으로 작명할 것인가?

</div>

* 1개의 정수와 1개의 실수와 1개의 캐릭터와 2개의 문자열 합 :
* 2개의 정수와 1개의 실수와 2개의 캐릭터와 1개의 문자열 합 :
* 3개의 정수와 1개의 실수와 4개의 캐릭터와 2개의 문자열 합 :

 . . .

* 5개의 실수 합 : 
* 6개의 실수 합 : 
* 7개의 실수 합 :

 . . .

<div>
모든 정보가 들어간 함수명이었다면 짧은 함수명으로 만들기 힘들었을 것이고, 짧았다면 약자나 넘버링을 함으로써 직관적인 함수명이 되기 힘들었을 것이다. 물론 이렇게 복잡하게 코드를 짜야할 경우는 잘 없겠지만, "오버로딩"이라는 기능으로 같은 함수명으로도 비슷한 기능을 하는 함수를 무한히 만들어 낼 수 있게 되면서 괴로운 작명 작업이 줄었다는 것이다. 물론 반대로 사용할 때도 간편하다. :-)(만세!)</div>

<h1>생성자 함수(Constructor)</h1>
<div>생성자 함수란, 객체화 할 수 있게 해주는 초기화 함수이며 다음과 같은 특징을 가진다.</div>
<li>인스턴스 변수를 가지고, 인스턴스 함수는 가지지 않는다.</li>
<li>정적 멤버 변수와 정적 멤버 함수를 가지지 않는다.</li>
<li>컴파일 과정에서 생성자 함수가 실행이 되고, 프로그램이 실행되는 동안 한 번만 실행된다.</li>
<li>컴파일러는 소스파일에서 생성자 함수를 찾지 못하면 default 생성자를 만든다.</li>
<div>생성자 함수를 적어 주지 않아도 컴파일러에 의해 default 생성자가 만들어지므로 코드는 오류없이 돌아가지만, 생성자로 초기화하는 것이 바람직하다.</div>

<h2>생성자 함수 오버로딩</h2>
<div>생성자 함수를 오버로딩 작성법이라고도 볼 수 있고 규칙이라고 생각해도 좋겠다. 아래의 사항들을 지키면 생성자 함수를 오버로딩하여 작성할 수 있다.</div>
<li>함수명 : 클래스 이름과 같게</li>
<li>리턴값 : void이며, 생략한다.</li>
<li>초기화 대상 : 인스턴스 변수. this 사용 유무에 대해선 다음 포스팅에서 다루겠다.</li>
<li>매개변수 : 입력받아 초기화에 사용할 매개변수들을 적어준다.</li>


<div>아래는 오버로딩을 활용한 클래스 예시이다. 오버로딩의 개념은 생성자 함수뿐만 아니라 함수에서 사용할 수 있다.</div>

```java
public class Sum {
	//클래스에서 사용할 인스턴스변수들 선언
	private int num1; private int num2; private int num3; private int sum;

	public /*void는 생략한다*/ Sum() { //클래스명과 같게 함수명을 적어준다.
		this.num1=0; this.num2=0; //인스턴스 변수를 초기화한다
        this.num3=0; this.sum=0;
	}
	public Sum(int num1, int num2) { //매개변수를 입력받아 초기화를 한다.
		this.num1=num1; this.num2=num2;
		this.sum=this.num1+this.num2;
	}

	/*인스턴스 함수의 오버로딩 예시*/
	public Sum(int num1, int num2, int num3) { //인스턴스 함수에서도 오버로딩!
		this.num1=num1; this.num2=num2; this.num3=num3;
		this.sum=0;
	}
    public int sumCalculator() {
		return this.num1+this.num2;
	}
   	public int sumCalculator(int num1, int num2) {
    	this.num1=num1; this.num2=num2;
		rreturn this.num1+this.num2;
	}
	public int sumCalculator(int num1, int num2, int num3) {
		this.num1=num1; this.num2=num2; this.num3=num3;
		return this.num1+this.num2+this.num3;
	}
}
```

```java
public class SumTest {

	public static void main(String[] args) {
		Sum sum = new Sum(); //위의 클래스에서 만든 생성자를 통해 객체화
        /*인스턴스 함수의 오버로딩 예시*/
		System.out.println(sum.sumCalculator(10, 20));
		System.out.println(sum.sumCalculator(10, 20, 30));
	}
}
```

