---
title: Java Serializable 정보를 파일로 저장하고 불러와서 출력하기 (feat.Serializable 상속받은 객체)
author: Yeonseo Nam
date: 2019-09-17 19:34:00 +0900
categories: [Java, Tutorial]
tags: [FileInputStream, FileOutputStream, ObjectInputStream,ObjectOuputStream, Serializable, Map, Algorism, code, Java, TIL, Today I Leaned]
comments : true
---

* Development Env.
* post date : 2019. 09. 17
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)

## Problem

* 사용자의 번호, 이름, 전화번호, 이메일 주소 등을 입력받아서 파일로 저장하기.
* 번호를 입력받아서 번호에 해당하는 전화 번호를 출력하는 프로그램을 작성하기.

### 객체 생성에 사용할 User Class

꼭 Serializable를 상속받아 ObjectInputStream,ObjectOuputStream를 사용할 수 있도록 한다.

```java
public class User implements Serializable{
	private String name;
	private String phone;
	private String email;
	public User(String name, String phone, String email) {
		super();
		this.name = name;
		this.phone = phone;
		this.email = email;
	}
	@Override
	public String toString() {
		return "User [name=" + name + ", phone=" + phone + ", email=" + email + "]";
	}
}
```

### Map에 사용자의 정보를 입력받고, 파일을 불러올 UserDataTest Class

```java
public class UserDataTest {

	public static void main(String[] args) throws IOException, ClassNotFoundException {
		Map<Integer, User> map = new HashMap<Integer, User>();
		putMap(map);
		save(map);
		print();
	}
	
	public static void putMap(Map<Integer, User> map) {
		map.put(1, new User("남영기","010-5252-5252","0ki_ZZang@naver.com"));
		map.put(2, new User("정미선","010-5252-5252","missunny@daum.net"));
		Scanner scan = new Scanner(System.in);
		System.out.print("몇명을 입력하시겠습니까? : ");
		int num = scan.nextInt();
		for(int i = 0 ; i < num ; i++) {
			System.out.print("이름을 입력하세요 : "); String name = scan.next();
			System.out.print("전화번호를 입력하세요 : "); String phone = scan.next();
			System.out.print("메일을 입력하세요 : "); String email = scan.next();
			int index = map.size()+1;
			map.put(index, new User(name, phone, email));
		}
	}
	
	public static void save(Map<Integer, User>map) throws IOException {
		File file = new File("Object.dat");
		FileOutputStream fos = new FileOutputStream(file);
		ObjectOutputStream oos = new ObjectOutputStream(fos);
		
		oos.writeObject(map);
		if(oos!=null) { oos.close(); }
		System.out.println("Done!");
	}
	public static void print() throws IOException, ClassNotFoundException {
		File file = new File("Object.dat");
		
		FileInputStream fis=new FileInputStream(file);
	    ObjectInputStream ois=new ObjectInputStream(fis);
	    Map<Integer, User>map =(Map<Integer, User>)ois.readObject();
	    String choice="";
		for(Map.Entry<Integer, User> elem : map.entrySet()){
            Integer key = elem.getKey();  User value = elem.getValue();
            System.out.println(key+" : "+value);
            if(key == 1 || key == 2 ||key == 3) {
            	choice+="Choice "+key+" : "+value+"\n";
            }
        }
		System.out.println(choice);
	}
}

```
\
