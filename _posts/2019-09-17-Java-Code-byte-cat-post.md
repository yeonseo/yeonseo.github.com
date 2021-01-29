---
title: 바이트 단위로 두개의 파일을 비교하기
author: Yeonseo Nam
date: 2019-09-17 16:34:00 +0900
categories: [Java, Tutorial]
tags: [FileInputStream, FileOutputStream, Algorism, code, TIL, Today I Leaned]
comments : true
---

* Development Env.
* post date : 2019. 09. 17
* OS : macOS Majave 64bit
* Java version : JDK 1.8.0 J_220 JRE8
* Eclipse : 2019-06 (4.12.0)

## Problem

* 바이트 단위로 비교하기
* 비교값이 틀리면 출력하기

```java
private static final Exception Exception = null;
public static void fileCat() throws IOException{
	FileInputStream fis1 = null;  FileInputStream fis2 = null;
	FileOutputStream fos = null;
        
        try {
            fis1 = new FileInputStream("dataProblemInput.txt");
            fis2 = new FileInputStream("dataProblemInput2.txt");
            fos = new FileOutputStream("fileCatResult.txt");

            int readData1 = 0; int readData2 = 0; 
				while(true){
            		readData1 = fis1.read();
            		readData2 = fis2.read();
            		if(readData1!=readData2||(readData1!=-1&&readData2==-1)) {
            			System.out.println("불일치~");
            			throw Exception;
            		}
            		if(readData1==-1) {
            			System.out.println("일치함미당!!!");
            			break;
					}
        } catch (Exception e) { e.printStackTrace();
        }finally{
            try { fos.close();
            } catch (IOException e) { e.printStackTrace(); }
            try { fis1.close(); fis2.close();
            } catch (IOException e) { e.printStackTrace(); }
            	System.out.println("Cat done");
        }
}
```

#### 같을 경우 결과

```java
일치함미당!!!
Cat done
```

#### 같을 경우 결과

```java
불일치~
Cat done
java.lang.NullPointerException
	at chapter17.problem.StringToList.fileCat(StringToList.java:143)
	at chapter17.problem.StringToList.main(StringToList.java:33)
```