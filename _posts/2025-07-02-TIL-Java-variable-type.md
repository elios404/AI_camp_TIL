---
layout: post
title: "7월 2일 수요일 TIL(자바 자료형)"
date: 2025-07-02 00:00:01 +0900
categories: July_week1
---

# 7월 2일 수요일 TIL 작성

## 1. 학습 주제
- 여러 변수 자료형 
- 전역변수/지역변수
- 형 변환

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### A. 실수 자료형
정수는 바이트가 커질수록 표현할 수 있는 값이 커지지만, 실수형은 표현의 `정밀도가 높아`진다.
float 실수는 뒤에  `f, F`를 적어주어야 한다.


### B. var 자료형
`var`를 통해서 변수를 정의할 수 있다. 

var의 특징
- 리터럴을 통해 자료 형을 추론
- 프로그램이 실행되고 처음 한 번만 추론 (이후 프로그램 종료 전까지 추론된 자료형에서 변하지 않음)
- 지역 변수로만 선언 가능

길이가 긴 이름을 짧게 쓸 수 있다.
```java
BigDecimal bdNum = new BigDecimal("0.1");	
//같은 경우
var bdNum = new BigDecimal("0.1");
// 같이 사용할 수 있다.
```

### C. 지역변수 / 전역변수
`{}`를 통한 코드 블럭(혹은 스코프) -> 블럭 내 : 지역변수, 블럭 외 : 전역변수

**변수는 코드 블럭 내에서만 메모리에 유지된다.**

메모리를 효율적으로 활용하고 싶다면, 코드 블럭으로 코드를 감싸 변수를 메모리에서 제거할 수 있다.

**파이썬**
자바처럼 `{}`로 코드블럭을 만들 수 없지만, 함수 들여쓰기로 블럭을 생성 가능. 

파이썬에서 코드를 함수로 작성해야하는 또 다른 이유! : 메모리

### D. 상수
`final double Pi = 3.14`와 같이 선언한다.

### E.형 변환
- 묵시적 형 변환 : 작은 수에서 큰 수록 자동으로 형 변환
- 명시적 형 변환 : `(int), (double)`와 같이 변수 이름 앞에 명시적으로 바꿀 형태 지정

이때 바이트 수와 상관 없이 실수는 정수 자료형 보다 큰 값으로 취급된다. 

`byte < short < int < long < float < double`

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)
### 실수의 부동소수점 오류
우리가 쓰는 10진수의 값을 2진수로 실수 변환을 하는 과정에서 작은 오차들이 생긴다.
```java
double errorNum = 0.1 + 0.1 + 0.1; 	//원하는 값 0.3
System.out.println(errorNum);		//실제 값 0.30000000000000004

// BigDecimal 로 문제 해결
import java.math.BigDecimal; //java.math(자바 기본 패키지).BigDecimal(클래스)

BigDecimal bdNum = new BigDecimal("0.1");	
//0.1로 적으면 자동으로 double로 처리되어(리터럴) 오류가 없어지지 않음.
//따라서 "0.1"로 문자열로 입력해서 값이 그대로 들어가게 한다.

BigDecimal result = bdNum.add(bdNum).add(bdNum);
System.out.println(result); //0.3 출력
```
자바에서는 Decimal을 사용하거나, 아니면 추후 DB를 통해서 계산한다.

### 형 변환이 안되는 경우
```java
String a1 = "1", b1 = "2";
System.out.println(a1 + b1);

// 에러가 발생
int a2 = (int)a1; // a1 : 참조 (주소값을 가짐)
int b2 = (int)b1; // b1 : 참조 (주소값을 가짐)
```
변환이 불가한 이유는 String과 int는 저장되는 메모리가 다르기 때문

기본 자료형인 int, double 등은 Stack 영역에 저장

숫자, 문자, 논리 : 기본형(stack) -> 값(stack), 변수명(stack)

클래스 자료형, 참조 자료형 (String, Integer) 은 Heap에 저장

문자열(클래스형) : 참조형(heap) -> 값(heap), 참조(주소, stack)

#### boxing 과 unboxing
```java
// heap 공간의 값을 --> stack 공간으로 가져오는 과정 : unboxing (중요!!)
// int -> Integer, double -> Double : 기본 자료형의 클래스들, Wrapper class
int a2 = Integer.parseInt(a1); 	//a1의 주소에 있는 힙 공간의 값을 가져온다.
int b2 = Integer.parseInt(b1); 	//b1의 주소에 있는 힙 공간의 값을 가져온다.
System.out.println(a2 + b2);		//이 방법을 통해서 문자열을 숫자처럼 처리할 수 있다.

// stack 공간의 값 -> heap 공간 : boxing
String s1 = String.valueOf(a2);
String s2 = String.valueOf(b2);
System.out.println(s1+s2);
```
`Unboxing`은 Heap 영역에 있는 값을 stack으로 가져오는 과정

`boxing`은 stack 영역의 값을 heap으로 보내는 과정

## 4. 참고 코드 / 예시

## 5. 참고자료 / 링크

## 6. 느낀 점 / 회고 
파이썬에서 코드를 함수로 적어야 하는 이유와 관련하여, 6월 기초 과정에서는 여러 사람이 협업을 할 때 변수 명 등이 겹치는 등의 문제를 막기 위해라는 설명을 들었다. 이는 함수 내에 지역변수로 사용하면 다른 함수나, 밖에서 같은 이름을 사용해도 문제가 발생하지 않기 때문에, 협업의 관점에서 본 이유.

그러나 오늘 자바를 통해서 새로운 메모리 관점에서 코드블럭이나 함수에 코드를 작성해야 하는 이유를 알게되어서 매우 새로웠다. 

또한 내가 단순히 쉽다고 넘어간 자료형에서 이런 다양하고 깊은 지식들을 알게 되어 배움에는 끝이 없다는 생각이 들었다.