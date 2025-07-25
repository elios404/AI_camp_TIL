---
layout: post
title: "7월 14일 월요일 TIL(자바 StringBuilder, enum, getClass(), 제너릭)"
date: 2025-07-14 18:00:01 +0900
categories: July_week3
---

# 7월 14일 월요일 TIL 작성

## 1. 학습 주제
- Object 클래스
- String, StringBuilder, StringBuffer
- enum / record 클래스
- Class 가져오기
- 제네릭 프로그래밍
- Collection framework(ArrayList, LinkedList)

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### Object 클래스
모든 클래스들이 상속 받는 최상위 클래스, 따라서 모든 클래스들은 컴파일러가 `extneds Object`를 자동으로 설정해준다. 모든 클래스들은 Object클래스의 메서드를 사용할 수 있고, 그 중 일부를 오버라이딩으로 재정의할 수 있다.

**가지고 있는 주요 메서드**
- String toString() : 객체를 문자열로 표현하여 반환한다. System.out.print()로 출력했을 때 나오는 값이다. 보통 클래스 내부 필드값을 출력하고자 할 때 오버라이드로 재정의하곤 한다.
- boolean equals(Object obj) : 두 인스턴스가 같은 주소를 가지는지 여부를 반환한다. 재정의하여 논리적으로 같은 값을 가지는지 비교하도록 정의할 수 있다. 기본적으로 주소가 같은지를 비교하지만, String,Integer 와 같은 클래스들은 값이 같은지 비교하는것으로 이미 오버라이딩 되어있다.
- int hashCode() : 객체의 해시코드 값을 반환한다. 해시는 정보를 저장, 검색하기 위해 사용하는 자료구조이다. 자료의 특정 값(키 값)에 대해 저장 위치를 반환해 주는 해시함수가 작동한다. 
- Object clone() : 객체를 복사하여 인스턴스 변숫값이 동일한 새로운 인스턴스 생성(얕은 복사), clone을 오버라이딩 하기 위해서는 클래스에서 `implements Clonable` 를 통해 인터페이스 구현을 해야한다.
- Class getClass() : 객체의 클래스를 반환합니다.

### String 클래스
기본적으로 String을 선언하는 두가지 방법이 있다.
1. `String str1 = new Stirng("abc");`
2. `String str2 = "abc";`

이 두 방식은 데이터를 String Pool에서만 가져오는가 아니면 Heap에도 그것을 올리는가 라는 차이점이 있다. 기본적으로 자바는 새로운 문자열에 들어오면 이를 String Pool(상수 풀의 종류)에 올리고 다음에 같은 문자열이 들어오면 **객체 인터닝(Object Interning)**을 통해서 처리한다. 객체 인터닝이란 특정 타입의 객체를 생성할 때 이미 동일한 값을 가진 객체가 메모리(풀)에 존재하면, **새로운 객체를 생성하지 않고 기존의 객체를 재사용**하는 기법이다.
```java
String str1 = "abc";
String str2 = "abc";

System.out.println(str1 == str2); //True
System.out.println(str1.equals(str2)); //True
```
보통 객체를 생성하면 객체는 서로 다른 메모리 주소를 가진다. 하지만 String은 객체 인터닝을 사용하기에 "abc"라는 같은 문자열에 대해 이미 메모리에 있는 값을 사용하기에 `str1 == str2`와 같이 주소값을 비교해도 같은 주소값을 가진다는 것을 확인할 수 있다. 또한 String의 equals() 메서드는 주소가 아닌 같은 값을 가지는지 확인하는 것으로 오버라이딩 되었기에, 만약 두 문자열의 주소가 다르더라도 안의 문자열이 같으면 True를 반환한다.

#### String의 여러 사용법
기본적으로 `""`를 통해서 받을 수 있는 문자열은 한 줄만 가능하다. 따라서 그 문자열 안에서 줄 바꿈이나 탭 등을 사용하려면 **이스케이프 문자(escape 문자)**를 활용해야 한다. `\n` : 줄 바꿈, `\t` : 탭 등이 있다.

그러나 `""" """`를 통해서도 자바에서 여러 줄에 걸쳐서 문자열을 작성할 수 있게 되었다. 이를 **Text Block**이라고 한다. 또한 문자열과 변수의 값 등을 함께 적을 때 사용할 수있는, **`formatted()`** 메서드가 존재한다. 예시 코드는 다음과 같다.
```java
String str7 = """
        안녕하세요. %s 님 
        반갑습니다.	 %d 회째 만남입니다.
        당신의 포인트는 %.1f 점입니다.
        다음에 또 만나요.
        """.formatted("홍길동",7,777.77);
```
`%s`는 문자열, `%d`는 정수형, `%f`는 실수형을 대체하는 문자이고 formatted에 들어가는 순서대로 문자열에 대입된다. 또한 실수 자료형 같은 경우 `.(숫자)`를 통해서 소수점 몇 자리까지 출력할 지를 지정할 수 있고 값은 반올림 되어 표시된다. 

#### String 문자열 연결과 StringBuilder, StringBuffer
String 자료형은 기본적으로 불변형이다. 한 번 특정 주소에 값이 지정이 되면, 그 주소에 있는 값은 더 이상 바꿀 수 없다는 뜻이다. 우리가 같은 변수에 대해서 다른 문자열을 대입하는 것도, 그 주소의 값을 바꾸는 것이 아닌, 새로운 문자열을 메모리에 올리고, 새로운 주소를 다시 가르키는 것이다.

이런 상황에서 `+`연산자를 통해서 여러 문자열을 이어준다고 생각하자. "a" + "b" + "c"를 할 때 우리는 한 번에 "abc"를 받는 것이 아닌 중간과정 "ab"를 만든 후 다시 "abc"가 만들어진다. 이 말은 중간과정 "ab"가 불필요하게 메모리에 올라온다는 말이다. 이러한 문제를 해결하고 문자열이 계속 바뀌는 상황에서 메모리 낭비를 막기위해 StringBuilder와 StringBuffer가 나왔다.

둘의 사용처는 거의 같지만 약간의 다른 특성을 비교해보면 다음과 같다.
- StringBuilder : 싱글 쓰레드에서 사용됨
- StringBuffer : 멀티 쓰레드에서 사용됨. 멀티 쓰레드 프로그래밍에서 동기화를 보장함

StringBuilder와 StringBuffer 는 String 객체는 아니기에 마지막에 toString()을 통해서 다시 String으로 바꿔주어야 한다.

하지만 **요즘 자바는 `+`연산자로 문자열을 이을 때 저절로 StirngBuilder를 호출한다.** 따라서 중간 과정에서 생기는 문자열은 메모리에 올리지 않고 바로 StringBuilder로 처리한 다음 자동으로 다시 String으로 바꿔서 결과물을 리턴해 준다.

## enum
상수 집합을 클래스 처럼 사용하는 것이 enum이다. 사실 정확한 사용법은 이해하지 못했지만, 상수만을 특별히 모아둔 클래스라고 이해하고 있다. enum과 일반 클래스의 차이는 enum은 상수로 선언한 클래스로 인스턴스 화 할 수 없다. enum은 데이터 영역에 존재하고 한 번 초기화된 값은 변경될 수 없다. 

**enum이 제공하는 메서드**
- values() : enum에서 선언된 모든 상수를 하나의 배열로 반환한다.
- toString() : 상수의 변수 이름을 반환한다. 재정의가 가능하다.
- valueOf(String) : 매개 변수 String과 같은 이름을 찾고 그 enum 객체를 리턴한다.

결국 enum은 상수만을 담는 특정 형태의 클래스를 정의한 후, 그 클래스 객체들을 자기가 담고 있고, 그 객체의 이름(상수 이름) 으로 특정 데이터 객체를 불러와("월요일", true) 사용하고자 하는 것이다.
**예시 코드** 를 보면서 이해해야 한다. 
```java
public enum Day {
    MONDAY("월요일", true),
    TUESDAY("화요일", true),
    WEDNESDAY("수요일", true),
    THURSDAY("목요일", true),
    FRIDAY("금요일", true),
    SATURDAY("토요일", false),
    SUNDAY("일요일", false);

    // enum 클래스인 Day가 어떤 값을 가질 수 있는가? (멤버변수)
    private final String koreanName;
    private final boolean isWeekday;

    // enum 클래스의 생성자, 실제 불러지는 것은 아닌 위에서 정의할 때 이 방식을 사용
    Day(String koreanName, boolean isWeekday) {
        this.koreanName = koreanName;
        this.isWeekday = isWeekday;
    }

    // 위에 실제 정의된 객체가 사용하는 메서드
    public String getKoreanName() {
        return koreanName;
    }

    public boolean isWeekday() {
        return isWeekday;
    }

    @Override
    public String toString() {
        return this.name() + " (" + koreanName + ")";
    }

    public static void main(String[] args) {
        // 1. 올바른 문자열로 enum 상수 얻기
        String dayString1 = "MONDAY";
        Day day1 = Day.valueOf(dayString1); // "MONDAY"에 해당하는 Day.MONDAY 상수를 반환
        System.out.println(dayString1 + " -> " + day1.getKoreanName() + ", 평일: " + day1.isWeekday());
        // 출력: MONDAY -> 월요일, 평일: true

        System.out.println("--------------------");

        // 2. 다른 올바른 문자열로 enum 상수 얻기
        String dayString2 = "SATURDAY";
        Day day2 = Day.valueOf(dayString2); // "SATURDAY"에 해당하는 Day.SATURDAY 상수를 반환
        System.out.println(dayString2 + " -> " + day2.getKoreanName() + ", 평일: " + day2.isWeekday());
        // 출력: SATURDAY -> 토요일, 평일: false

        System.out.println("--------------------");

        // 3. 존재하지 않는 이름을 사용할 경우 (예외 발생)
        String invalidDayString = "FUNDAY";
        try {
            Day invalidDay = Day.valueOf(invalidDayString); // IllegalArgumentException 발생
            System.out.println("이 메시지는 출력되지 않습니다: " + invalidDay);
        } catch (IllegalArgumentException e) {
            System.out.println("에러 발생: '" + invalidDayString + "'은(는) 유효한 요일이 아닙니다.");
            // 출력: 에러 발생: 'FUNDAY'은(는) 유효한 요일이 아닙니다.
        }

        System.out.println("--------------------");

        // 4. 대소문자가 일치하지 않을 경우 (예외 발생)
        String wrongCaseDayString = "monday"; // MONDAY와 다름
        try {
            Day wrongCaseDay = Day.valueOf(wrongCaseDayString);
            System.out.println("이 메시지는 출력되지 않습니다: " + wrongCaseDay);
        } catch (IllegalArgumentException e) {
            System.out.println("에러 발생: '" + wrongCaseDayString + "'은(는) 유효한 요일이 아닙니다 (대소문자 불일치).");
            // 출력: 에러 발생: 'monday'은(는) 유효한 요일이 아닙니다 (대소문자 불일치).
        }
    }
}
```

## Class 가져오기
Class 를 가져오는데는 3가지 방법이 있다.
1. 클래스로 만든 객체에 getClass() 메서드 사용하기. 
2. 클래스 파일의 이름을 class 변수에 직접 대입하기
3. Class.forName("클래스 이름") 메서드 사용하기

코드를 통해 확인하기
```java
package classex; //Person 클래스가 있음

public class ClassTest {
    public static void main(String[] args) throws ClassNotFoundException {
        Person person = new Person();
        Class pClass1 = person.getClass();
        System.out.println(pClass1.getName()); // classex.Person 출력

        Class pClass2 = Person.class;
        System.out.println(pClass2.getName()); // classex.Perosn 출력

        class pClass3 = Class.forName("classex.Person");
        System.out.println(pClass3.getName()); // classex.Perosn 출력
    }
}
```

`Class` 라는 이름을 가진 클래스는 특별하게, 클래스의 설계도를 담는 자료형이라고 보면 된다.'메타클래스'라고 부르기도 한다. `Person.class;` 를 통해서 우리는 Person 이라는 클래스의 메타데이터, 즉 설계도로 만든 객체가 아닌 설계도 그 자체를 받는다. 이때 `Class`는 우리가 받는 설계도를 담는 자료형인 것이다. (이것은 나의 이해를 위한 예시, 정확한 설명은 아닐 수 있음.)

### reflection 프로그래밍
reflection 프로그래밍은 Class 가져오기를 통한 `Class` 클래스를 이용하여 어느 객체(클래스)의 정보(생성자, 멤버변수, 메서드)를 가져오고, 이를 활용하여 인스턴스를 생성하거나, 메서드를 호출하는 등의 프로그래밍 방식이다.

우리는 로컬 메모리에 객체가 없어서 객체의 데이터 타입을 직접 알 수 없는 경우(원격에 객체가 있는 경우 등) 객체 정보만을 이용하여 클래스 설계도(구조)를 찾아낼 수 있다.

이렇게 알아낸 클래스의 설계도를 Class 라는 자료형의 변수로 받고 이를 통해서 생성자를 가져오고, 인스턴스 변수, 메서드 등을 접근할 수 있다. 또한 이 Class 를 통해서 newInstance() 메서드를 쓰면 실제 내 메모리에 그 객체의 설계도가 없어도, 이 과정을 통해서 새로운 객체를 생성할 수 있다.

> 아직은 어려운 내용일 수 있다. 이런게 있다는 것을 알아두고 나중에 활용할 수 있도록 하자.


## 제너릭 프로그래밍
제너릭 프로그래밍(Generic Programming)은 변수의 선언이나 메서드의 매개변수를 하나의 지정된(Fix) **참조 자료형이 아닌**(기본 자료형은 포함이 아예 안된다.) 여러 자료형으로 변환 될 수 있도록 프로그래밍 하는 방식이다. 여러 참조 자료형으로 대체될 수 있는 부분을 하나의 문자로 표현하고(이때 T를 많이 사용) `<>` 다이아몬드 연산자를 사용하여 표현한다. 또한 static 키워드와 함께 사용할 수 없다.

### 제너릭 클래스

```java
public class Box<T> { // <T>는 이 클래스가 제네릭 클래스이며, T는 타입 파라미터임을 의미합니다.
    private T content; // T 타입의 content를 저장합니다.

    public void setContent(T content) { // T 타입의 content를 설정합니다.
        this.content = content;
    }

    public T getContent() { // T 타입의 content를 반환합니다.
        return content;
    }

    public static void main(String[] args) {
        // String을 담는 Box 생성
        Box<String> stringBox = new Box<>();
        stringBox.setContent("Hello Generics!");
        String message = stringBox.getContent(); // 별도의 형변환 필요 없음
        System.out.println("String Box: " + message);

        // Integer를 담는 Box 생성
        Box<Integer> integerBox = new Box<>();
        integerBox.setContent(123);
        int number = integerBox.getContent(); // 별도의 형변환 필요 없음
        System.out.println("Integer Box: " + number);

        // 컴파일 시점에 타입 안전성 확인 (오류 발생)
        // stringBox.setContent(123); // 에러: Integer를 String Box에 넣을 수 없음
    }
}
```
다음 코드를 통해서 제너릭에 대해서 명확하게 알 수 있다. Box라는 블래스는 실제 어떤 참조 자료형을 내부에서 사용할 지 아직 정하지 않은 상태로 `<T>`를 통해서 임시로 지정하였다. 그리고 실제 Box를 사용할 때 <> 를 통해 어떤 참조 자료형을 사용할 것인지 정해줄 수 있게 된다.

또한 `<T extends '클래스/인터페이스 이름'>`을 통해서 T 여러 참조자료형 중에서도 어떤 것들이 올 수 있는지 제한할 수 있다. 이 경우 그 클래스/인터페이스 와 그것을 상속받은 자식 클래스 형식만 들어올 수 있다.

### 제너릭 메서드
메서드에서 또한 사용할 수 있다. 메서드의 매개변수를 자료형 매개변수로 사용하는 경우, 자료형 매개 변수가 하나 이상인 경우 사용할 수 있다.
보통 `public <T> void add(T num1, T num2) { }`와 같이 사용 가능하다.

```java
public class GenericMethodSimpleExample {

    // <T> : 이 메서드가 제네릭 메서드임을 선언하고, T가 타입 파라미터임을 나타냅니다.
    // void : 메서드의 반환 타입입니다.
    // T value : T 타입의 매개변수를 받습니다.
    public static <T> void printValue(T value) {
        System.out.println("전달받은 값: " + value);
        System.out.println("값의 타입: " + value.getClass().getName());
        System.out.println("--------------------");
    }

    public static void main(String[] args) {
        // String 타입의 값을 전달
        printValue("안녕하세요!"); // T는 String으로 추론됩니다.

        // Integer 타입의 값을 전달
        printValue(12345); // T는 Integer로 추론됩니다.

        // Double 타입의 값을 전달
        printValue(3.14); // T는 Double로 추론됩니다.

        // Boolean 타입의 값을 전달
        printValue(true); // T는 Boolean으로 추론됩니다.
    }
}
```
이와 같이 T에 어떤 값이 들어갈지 추후에 들어가는 값을 바탕으로 설정할 수 있다. 다만 이때도 마찬가지로 **참조 자료형(class)** 밖에 들어가지 못하기에, 기초 자료형과 다르게 사용되는 부분을 신경써서 구현해야한다. 또한 제너릭 메서드를 하나만 사용할 수 있는 것은 아니고 `<T,V>` 와 같이 둘 이상의 제너릭 자료형을 받을 수 있다.


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

## 4. 참고 코드 / 예시

## 5. 참고자료 / 링크

## 6. 느낀 점 / 회고 