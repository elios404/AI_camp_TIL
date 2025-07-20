---
layout: post
title: "7월 8일 화요일 TIL(자바 클래스 this, Builder D.P.)"
date: 2025-07-08 00:00:01 +0900
categories: July_week2
---

# 7월 8일 화요일 TIL 작성

## 1. 학습 주제
- 자바 클래스 this의 개념
- this 활용 메서드 체이닝
- Builder 디자인 패턴

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### this 의 개념
자바의 클래스에는 this라는 개념이 있다. 이는 클래스를 사용할 때만 중요한 것이 아니라, this를 활용한 메서드 체이닝, 나아가 빌더 디자인 패턴에 매우 중요한 역할을 하기에 정확하게 이해하고 넘어가는 것이 중요하다.

#### this가 하는 일
- 자신의 메모리 주소를 가리킨다.
- 생성자 내부에서 다른 생성자를 호출한다.
- 자신의 메모리 주소를 반환한다.

일단 첫 번째로 this는 클래스 내 생성자나 메서드에서 매개변수와 멤버변수의 이름이 같을 때, 혼동을 피하고 그 객체의 멤버변수를 정확하게 지정하기 위해서 사용한다. 객체가 있을 때 그 자신의 주소를 가르키고 그 메모리 주소에 있는, 자신이 가지고 있는 멤버변수라는 것을 명확하게 표현하기 위해 사용한다. 

클래스 내에서 this를 자신을 가르킬 때 this를 사용할 수 밖에 없는 이유는 다음과 같다. 우리는 일반적으로 클래스 변수를 만들고 메모리에 있는 객체의 주소값을 변수에 저장하여 사용한다. 하지만 이렇게 객체가 만들어 지기 전, 클래스를 설계하는 코드를 작성할 때, 혹은 객체를 생성하는 과정에서 아직 메모리 주소를 변수에 넣기 전이라면 자신을 어떻게 가르켜야 할까? 그렇기에 this라는 것을 만들고 객체에서 this를 호출하면 무조건 자기의 주소를 반환할 수 있도록 키워드를 지정해 놓는 것이다.

두 번째로 내부에서 다른 생성자를 호출할 때 사용한다.

```java
public Person() {
    this("empty", 1); //자기 자신의 생성자를 호출하기
    // 생성자 오버로딩(같은 이름의 메서드를 중복해서 생성), 매개변수로 구분함.
}

public Person(String name, int age) {
    this.name = name;
    this.age = age;
}
```
이와 같이 하나의 생성자를 이미 생성해 놓았다면 다른 생성자를 정의하는 과정에서 this를 통해서 이미 정의해 놓은 생성자를 사용할 수 있다. 이는 메서드 오버로딩이고 this를 사용할 때 매개변수가 어떻게 입력 되었는지에 따라서 어떤 생성자를 활용할지가 결정된다.

마지막으로 this는 자신의 주소값을 나타낸다.
```java
return this // 자기 자신을 반환한다.
```
이는 추후 기술할 메서드 체이닝과 Builder 디자인 패턴에서 주요하게 사용되는 기능이다.

### 메서드 체이닝

```java
public class ReturnThisEx {

	public static void main(String[] args) {
		
		Student stud = new Student(); //기본 생성자 사용으로 멤버 필드 값 초기화 안됨.
		stud.setId(12345)
			.setName("James")
			.setGrade(1); //return this를 하기에 이어서 메서드 체이닝을 할 수 있다.
		// 여전히 만든 다음 초기화 한다. 만들 때 바로 초기화 할 수는 없을까? -> Builder 디자인 패턴으로 발전
		
		System.out.println(stud.toString());

	}

}

class Student {
	private int id;
	private String name;
	private int grade;
	
	// getter
	public int getId() {
		return id;
	}
	public String getName() {
		return name;
	}
	public int getGrade() {
		return grade;
	}
	
	//setter : 기본적으로 setter는 void 리턴, 리턴을 하지 않는다. 그러나, 이를 Student로 바꾸고 this(자기 자신)을 리턴한다.
	// 자기 자신을 리턴하는 이유는 메서드 체이닝을 활용하기 위해서!!
	public Student setId(int id) {
		this.id = id;
		return this;
	}
	public Student setName(String name) {
		this.name = name;
		return this;
	}
	public Student setGrade(int grade) {
		this.grade = grade;
		return this;
	}
	
	@Override
	public String toString() {
		return "Student [id=" + id + ", name=" + name + ", grade=" + grade + "]";
	}
}
```
이 코드를 통해서 this를 활용한 메서드 체이닝을 설명할 수 있을것이라 생각한다. 기존의 set 메서드는 멤버 변수를 수정한 후 리턴을 하지 않고 종료한다. 하지만 만약 자기 자신을 반환한다면?

`stud.setId(12345).setName("James")` 은 `stud.setId(12345)`가 실행되어 자기자신 `stud`를 반환한다. 그럼 이는 `stud.setName("James")` 로 바뀌는 것이다. 이처럼 자기 자신을 반환하는 것으로 메서드를 게속해서 진행할 수 있다.

### Builder 디자인 패턴
`builder 패턴`은 생성자를 대신해 객체를 생성하기 위해서 사용하는 패턴이다. 즉 객체를 생성하고 값을 초기화 하는데 사용하는 패턴이다. 이 빌더 패턴은 앞써 나온 this. 를 통한 메서드 체이닝을 활용하여 구현된다. 

```java
// 생성자가 아닌 builder 를 이용하여 객체를 생성하는 방법
public class Student {
	
	private int id;
    private String name;
    private int grade;
    private String major;
    private String phoneNumber;
	
    // 매개변수로 들어가는 빌더도 클래스인데 new 를 안하고 선언되는 이유는 static 이기 때문에?
    // 이 생성자 자체는 private 이기에 사용할 수 없다. builder 의 추가 메서드를 통해서만 가능하다.
    private Student(Builder builder) {
        this.id = builder.id;
        this.name = builder.name;
        this.grade = builder.grade;
        this.major = builder.major;
        this.phoneNumber = builder.phoneNumber;
    }
    
    // 클래스 안에 클래스, 중첩 클래스 / Static 키워드를 쓰면 new를 쓰지 않고도 메모리에 올릴 수 있다.
    public static class Builder {
    	// 내부의 빌더 클래스는 외부의 클래스와 같은 멤버변수를 가진다.
        private int id;
        private String name;
        private int grade;
        private String major;
        private String phoneNumber;

        // 무조건 들어가야 하는 변수, 생성자로 초기화
        public Builder(int id, String name) {
            this.id = id;
            this.name = name;
        }

        // 이 아래는 전부 set, 어처피 전부 set이기에 앞에서 set제거함. (set)grade와 같다고 보면 됨.
        public Builder grade(int grade) {
            this.grade = grade;
            return this;
        }

        public Builder major(String major) {
            this.major = major;
            return this;
        }

        public Builder phoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
            return this;
        }

        public Student build() {
            return new Student(this); // 정리해 보기, 왜 new 를 2번 쓰나요?
        }
    }

    public static void main(String[] args) {
        Student student = new Student.Builder(1, "John Doe") // 이 친구는 builder 클래스를 부른다.
                .grade(12)
                .major("Computer Science")
                .phoneNumber("123-456-7890")
                .build();
        System.out.println(student.toString());

    }

}
```
builder 패턴의 작동 과정은 다음과 같다.

 `new Student.Builder`를 통해서 Student 클래스 안에 있는 builder 객체를 생성하고 메모리에 올린다. 이때 Student 객체가 생성되는 것이 아니다! builder class를 static으로 정의하였기에 Student를 생성하지 않고도 사용이 가능하다. 
 
 그 뒤 builder의 생성자를 활용해 생성을 하고 메서드 체이닝을 통해서 Builder의 필드값을 초기화 한다. 그 뒤 builder 클래스의 build() 메서드를 통해서 Student 클래스의 생성자를 호출한다. 이때 Student의 생성자는 private로 밖에서는 Student를 생성할 수 없고 무조건 builder를 거쳐 Studnet를 생성하게 한다.

 Student의 생성자는 builder를 매개변수로 받았다. 그리고 Student 클래스는 builder 안에 있는 멤버 변수 값을 사용하여 자신의 멤버 변수 값을 초기화 한다. 그리고 이 과정이 이루어 지기 위해서는 **내부의 builder 클래스는 자신이 속한 클래스의 모든 멤버 변수를 같이 가지고 있어야 한다.**

**그렇다면 왜 `builder 패턴`이 나왔을까?**

먼저 생성자를 통해서 객체를 생성하고 값을 초기화 하는 과정을 살펴보면, 생성자에 매개변수를 주어 값을 초기화 하는 경우, 생성자에서 정의한 대로 값의 순서를 정확하게 지켜 입력해 주어야 한다. 이는 곧 입력 순서를 외워야 한다는 것이다. 또한 값을 입력 하면서 이 값이 어떤 값인지를 정확하게 알 수 없다. 실제 괄호 안에는 값만 넣고 그것이 어떤 값인지를 표현해주는 방법이 들어가지 않아서, 이 값이 어떤 값을 넣은 것인지 확인하기가 어렵다.

다음으로 위에서 활용한 메서드 체이닝을 활용할 수 있다. 이 방법은 겉보기에는 builder 패턴과 그리 달라 보이지 않는다.

```java
// 메서드 체이닝 사용
Student stud = new Student(); 
stud.setId(12345)
    .setName("James")
    .setGrade(1);

// 빌더 패턴 사용
Student student = new Student.Builder(1, "John Doe")
                .grade(12)
                .major("Computer Science")
                .phoneNumber("123-456-7890")
                .build();
```
하지만 builder 패턴의 특징은 객체를 생성하는 코드와 값을 초기화 하는 코드가 분리되지 않고, 하나로 통합되어 있다는 것이다.

```java
Student stud1 = new Student();
// ... stud1 초기화 코드
Student stud2 = new Student(); 
stud1.setId(12345) // 이 부분에서 원래는 stud2를 초기화 해야 하는데?
    .setName("James")
    .setGrade(1);
```
위와 같은 코드를 보면 stud2를 초기화 해야 하는 상황에서, 사람의 실수, 오타, 등 여러 이유로 예상하지 못한 값의 초기화, 변경이 일어날 수 있다. 결국 프로그램의 예측 가능성이 떨어진다는 의미이다.
하지만 builder 패턴은 객체의 생성과 동시에 값을 초기화 하기에 이런 실수가 벌어질 가능성이 없어진다. 즉 프로그램의 예측 가능성을 높인다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### 왜 생성자 오버로딩을 할 때 this를 사용하는가?
처음에 this를 사용해서 한 생성자에서 다른 생성자를 불러올 때 의문이 생겼다. 왜 this를 사용해야 하지? 그냥 다른 생성자를 그대로 사용하면 되지 않을까? 즉 `this("이름 없음",1);` 이런 식이 아니라. `Person("이름없음", 1);` 이렇게 작성할 수는 없는 것일까? 결론부터 말하자면 불가능 하다.

생성자 코드 블럭 안에 `Person("이름없음", 1);`와 같이 작성하면 자바 컴파일러는 이를 매서드 호출로 해석하려고 한다. 하지만 이 코드는 메서드이름이 아니고 클래스 이름, 생성자 이름이다. 또한 메서드 이지만 반환 타입이 지정되어 있지 않다. 마지막으로 생성자 메서드는 new와 함께 호출되어 새로운 객체를 생성하는데 이 방식은 new가 없기에 새로운 객체를 생성하라는 의미가 되지 않는다.

그리고 그냥 설계를 할 때 this로 활용하도록 설계가 되어있다. `this`는 자바 문법에서 자신의 주소값을 불러오는 기능과 **생성자 내부에서 다른 생성자를 호출하기 위해서 특별하게 정의된 구문** 이다. 따라서 this 뒤에 괄호와 인자를 붙여서 활용해야 한다.


## 4. 참고 코드 / 예시

## 5. 참고자료 / 링크

## 6. 느낀 점 / 회고 
가면 갈 수록 배움의 깊이가 깊어지는 느낌이 있어서 즐겁다. 새로운 것을 배우는 것은 항상 즐거운 것 같다. 그리고 학습을 하면서 최대한 깊게 이해할 수 있도록 노력하고 있다. 단순히 외우는 것이 아니라, 왜 그렇게 되는지, 궁금한 게 있다면 끝까지 물고 늘어지고 나만의 해답을 얻을 수 있도록 공부하고 있다. 그리고 이를 기록하고 있다. 나의 이런 노력이 나중에 취업, 더 나아가 나의 개발자 인생에 탄탄한 기초가 되었으면 좋겠다.