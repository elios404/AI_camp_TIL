---
layout: post
title: "7월 3일 목요일 TIL(자바 연산자,문자열,조건문)"
date: 2025-07-03 00:00:00 +0900
categories: July_week1
---

# 7월 3일 목요일 TIL 작성

## 1. 학습 주제

논리 연산자, 문자열 연결, 조건문을 통한 흐름 제어

**객체지향 프로그래밍 / 함수형 프로그래밍**


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### A. 논리 연산자 & 와 && 비교 (| / || 도 동일)
| 특징 | &(단순 논리 AND) | && (쇼트 서킷 논리 AND) |
| --- | --- | --- |
| 우항 평가 | 좌항의 결과와 상관 없이 항상 우항을 평가 | 좌항이 False이면 우항을 평가하지 않음 |
| 주된 용도 | 비트연산 또는 모든 조건식을 평가해야할 때 | 효율성을 높이고, NullPointerException 등 오류 방지시 |
| 성능 | 경우에 따라 && 보다 비효율적 | 좌항이 false일 때 더 빠름 |

&& 혹은 || 의 경우 앞의 항을 통해서 연산의 결과가 확정이 된다면, 뒤에 것은 처리하지 않는다!

**예시**
```java
// 단락회로 연산자
num = 10;
int i = 2;

boolean value = ((num = num + 10) < 10) && ((i = i + 2) < 10);
System.out.println(value);
System.out.println(num); // 좌항이 실행되어 값 변화 있음
System.out.println(i); // 뒤 쪽이 실행되지 않기에 i는 변화 없음

value = ((num = num + 10) > 10) || ((i = i + 2) < 10);
System.out.println(value);
System.out.println(num); // 좌항이 실행되어 값 변화 있음
System.out.println(i); // 뒤 쪽이 실행되지 않기에 i는 변화 없음
```

### B. 삼항 연산자
```java
// 조건 연산자, 3항 연산자 조건 ? 참일 때 : 거짓일 때
int father = 5, mother = 3;
num = (father>mother) ? 100 : 5;
System.out.println(num);
```

간단한 조건문을 대신하는 역햘로 삼항 조건 연산자를 활용할 수 있다!

파이썬에서는 `A if (조건) else B` 와 같이 활용 가능하다.

### C. 문자열 연결
```java
age = 10
System.out.println("나이는 " + age + "살 입니다. 학교에 다닙니다.");
```
파이썬에서는 `,`를 통해서 변수들을 문자열로 연결하였지만, 자바에서는 `+`를 이용한다.

`+`는 자바에서 2가지 역할을 하는데, 숫자형 데이터들의 더하기 연산과 문자열 연결이다.

`+`가 처리하려는 자료형 중 1개라도 `String`형이라면 자동으로 문자열 연결로 처리되고 다른 자료형도 묵시적으로 `String`형으로 변환된다.

### D. Switch 문 (Java)

이전 코드할 때는 기본적으로 `if`를 활용하여 조건문을 작성하였다. 그러나 `Switch`문에 점점 여러 기능이 추가되면서 활용하기 편리해 졌다.

**추가된 기능**
- break 문을 사용하지 않도록
- 숫자 뿐 아니라 문자열도 비교 가능
  
---

```java
switch(ranking) {
	case 1 -> medalColor = "G";
	case 2 -> medalColor = "S";
	case 3 -> medalColor = "B";
	default -> medalColor = "A";
}
		
// -> 는 ; 를 만날 때 까지만 실행 됨.
```
기존에 매 `case`마다 `break`를 작성해야하는 점을 없에고 `->` 라는 연산자를 추가했다. 그러나 이 경우 `;`가 나오는 1줄만 실행 가능하다.
```java
int month = 7;
int day = switch(month) {
	case 2 -> 28;
	case 4,6,9,11 -> 30;
	case 1,3,5,7,8,10,12 -> 31;
	default -> {
		if (month < 1 || month > 12) {
			System.out.println("잘못된 월 입니다.");
		}
		else {
			System.out.println("알 수 없는 오류 입니다.");
		}
		yield 0;
	}
};
System.out.println(month + "월의 날짜는 " + day + "일 까지 있습니다.");
```
이 코드는 switch의 표현식과 여러 줄 실행 가능하도록 작동하는 코드이다.
Switch문을 통해서 결과를 변수에 저장할 수 있도록 표현형으로 작성 가능하고, 코드 블럭을 통해서 여러 줄을 실행 할 수 있다.

이 때 `yield 0`를 확인할 수 있는데, `day`변수에 대입할 값이 필요해서 함수에서 사용하는 `retrun`이 아닌 `yield`를 사용했다.

만약 반환할 필요가 없다면 꼭 사용해야 하는 것은 아니다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

## 4. 참고 코드 / 예시

## 5. 참고자료 / 링크

문제를 푸는 방식은 정말 여러 개라는 것을 느꼈다.
```java
int ranking = 1 // 어떤 값 입력
medal = switch(ranking) {
	case 1 -> "G";
	case 2 -> "S";
	case 3 -> "B";
	default -> "A";
};

System.out.println(medal);
```
이렇게 푸는 문제를 자료구조를 이용하면
```java
// 배열로 메달 색상을 정할 수 있다.

int ranking = 1 // 어떤 값 입력
String[] medals = {"","G","S","B","A"};
System.out.println(medals[(ranking > 3)?4:ranking]);
```

이렇게 감탄스러울 정도로 깔끔한 코드를 작성할 수 있다. 약간 파이썬스러운 코드..

## 6. 느낀 점 / 회고 

기본에 대해서 많은 것을 느낄 수 있는 하루였다. 내가 다 알고있는 조건문에서도 이렇게 내가 모르는게 많고, 내 생각보다 훨씬 더 단순하게, 효율적으로 작성할 수 있는 코드들이 많다는 것을 느껴서 경외감도 있었지만, 한 편으로는 약간의 두려움도 느껴졌다. 내가 이렇게 할 수 있을까? 마지막 하나의 예시
```py
s = 0
for i in range(1,11):
    s += i
# 와 같은 코드는

s = sum(range(1,11))
# 로 작성할 수 있다.
```
아래 코드가 훨씬 아름답고 성능도 좋다. 이렇게 아름다운 코드를 작성할 수 있도록 더욱 노력해야 겠다.