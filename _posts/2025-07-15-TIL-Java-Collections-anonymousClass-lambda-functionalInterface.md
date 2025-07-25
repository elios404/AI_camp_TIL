---
layout: post
title: "7월 15일 화요일 TIL(자바 Collections, inner/anonymous class, lambda, functional Interface)"
date: 2025-07-15 18:00:01 +0900
categories: July_week3
---

# 7월 15일 화요일 TIL 작성

## 1. 학습 주제
- Collection / Map 프레임 워크
  - ArrayList
  - LinkedList
  - HashSet
  - HashMap
  - 외 여러가지
- 내부 클래스
- 람다식
- 스트림

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### 리스트 / 셋
List/Set은 키와 값 쌍으로 가지는 것이 아닌 단순한 **값**만 저장된다.
그중 List는 중복 허용, 입력 순서 유지, 인덱스가 있음 이라는 특징이 있고, Set은 중복 불가, 입력 순서 유지 안함, 인덱스 없음 의 특징을 가진다.

#### list
ArrayList에 동기화 기능이 추가되어야 하는 경우
`Collections.synchronizedList(new ArrayList<String>());` 를 사용할 수 있다.
또한 LinkedList는 값에 더해 앞 뒤 주소를 전부 가지고 있는 이중연결 리스트이다.

#### 리스트의 주요 메서드
- .add(값/객체) : List 값(객체)를 추가한다.
- .size() : List 의 요소의 개수를 반환한다.
- .indexOf(값/객체) : List서 값(객체)의 인덱스 주소를 반환한다. 없다면 -1을 반환한다.
- .set(인덱스, 값) : List의 인덱스 위치의 값을 새로운 값으로 변경
- .remove(인덱스) : List의 인덱스 위치 값 제거
- .clear() : List의 전체 값 초기화
- forEach((item) -> System.out.println(itme)); : forEach와 람다식을 이용하여 리스트 안의 요소를 순차적으로 출력

**ArrayList와 LinkedList의 차이**
ArrayList는 메모리에 한 공간에 이어져서 할당된다. 그래서 값 입력이 할당된 공간을 넘어서면, 기존 공간의 1.5~2배정도(자세한 수치는 구현에 따라 다름)의 새로운 공간을 할당하고, 기존의 값을 전부 깊은 복사하는 식으로 작동하여 이때 시간 소모가 발생할 수 있다. 또한 ArrayList는 인덱스로 값을 접근하는데, 중간에 값이 추가되거나, 값 삭제가 일어나는 경우 변경된 인덱스 위치부터 뒷 부분을 전부 수정해야하기에, 값의 수정과 삭제가 빈번한 경우, 마찬가지로 시간 소모가 커진다.

반면 LinkedList에서는 값의 추가나 삭제에 따른 시간 소모가 거의 없다. 그 이유는 LinkedList의 저장 방식 차이 때문인데, 각 값은 앞의 주소값과 뒤 쪽 주소를 가지고 있기에 메모리 상에서 한 군데 할당될 필요가 없고, 삭제와 추가가 발생하는 경우에도 변경된 인덱스의 앞 뒤 값들만, 가지고있는 앞 뒤 주소값을 변경해 주면 되기에 시간 소모가 적다. 반면 LinkedList는 값을 찾아가는데 시간 소모가 크다. ArrayList의 경우 인덱스로 주소 위치를 바로 계산할 수 있는 반면, LinkedList는 처음부터 순차적으로 주소값을 따라서 탐색해야 하기에 O(n)의 시간 복잡도를 가진다.

**리스트의 추가 코드**
```java
// 생성하자 마자 초기화가 가능하다. (ArrayList 도 동일하게 사용 가능)
List<Integer> linkedList = new LinkedList<>() {
  {add(1); add(2); add(3); add(4); add(5);} // 초기화 블록 - 이중 괄호 초기화 - 익명클래스 사용
};

linkedList.forEach((item) -> System.out.println("for each : " + item)); // forEach와 람다식 사용
```

#### Set
Set은 중복을 허용하지 않는 자료을 저장하는 자료형이다. 또한 List는 순서 기반 인터페이스이지만, Set은 순서가 없다. 따라서 get(i)와 같이 인덱스로 값을 가져오는 메서드도 사용할 수 없다.


### 맵(Map)
리스트/셋과 달리 맵은 Key-Value의 쌍으로 이루어진 객체, 값을 저장하기 위해서 사용된다. 이때 Key 값은 중복될 수 없고, Value는 중복 가능하다. 또한 HashMap은 키 값을 hash값으로 저장하기에 키를 통해서 값을 찾고자 할 때 O(1)의 시간복잡도로 매우 빠르게 값을 찾을 수 있다.

여러 종류가 있지만 주로 알아볼 것은 HashMap과 LinkedHashMap이다. LinkedHashMap은 입력된 값의 순서가 유지되고, 그냥 HashMap은 입력 값의 순서가 유지되지 않는다.

#### 맵의 주요 메서드
- .put(K key, V value) : key에 해당하는 value값을 map에 넣는다.
- .get(K key) : key에 해당하는 value값을 반환한다.
- .isEmpty() : map이 비었는지 여부를 반환한다.
- .constainsKey(K key) : map에 키가 있는지 여부를 반환한다.
- .constainsValue(V value) : map에 value가 있는지 여부를 반환한다.
- .keySet() : map의 key 집합을 Set자료형으로 반환한다.(key는 중복 없음)
- .values() : map의 value를 Collection으로 반환한다.(중복 무관)
- .size() : map의 크기를 반환한다.
- .remove(K key) : key가 있는 경우 삭제한다.
- .remove(K Key, V value) : key가 있는 경우, key에 해당하는 value의 값도 같은 경우 삭제한다.


### 내부 클래스
클래스 내부에 혹은 메서드 내부에 선언한 클래스를 내부 클래스라고 한다.

인스턴스 내부 클래스는 외부 클래스 생성 후에 생성할 수 있고, 외부클래스의 참조 변수를 이용하여 내부 클래스를 사용 가능하다. 단순하게 클래스 안에 클래스를 선언하여 외부 클래스가 생성된 이후로는 다른 클래스 처럼 활용가능하고 단순히 접근만 외부 클래스를 통해서 하는 클래스이다. 이때 외부 클래스에는 내부 클래스형 변수를 선언하여 내부클래스를 담을 공간을 마련해야한다. 

정적 내부 클래스(static inner class)는 외부 클래스의 생성과 무관하게 사용할 수 있다. 하지만 정적 내부클래스는 Static이기에 외부 클래스가 정해지지 않아도 사용할 수 있어야 하고 그 말은 외부 클래스의 인스턴스 변수는 사용할 수 없다.

지역 내부 클래스(local inner class)는 지역 변수와 같이 메서드 내부에서 정의하여 사용하는 클래스이다. 이 클래스는 메서드를 호출 할 때 생성된다.

**익명 내부 클래스(anonymous inner class)**는 이름이 없는 클래스이다. 주로 하나의 인터페이스나 하나의 추상 클래스를 구현하는데 사용하는데, 인터페이스나 추상클래스의 추상 메서드를 변수형으로 직접 코드를 대입하거나, 람다식을 추상 메서드에 대입할 때 컴파일러에 의해 자동으로 선언된다.

익명 클래스는 이해가 조금 어려웠다. 그래서 코드를 통해 간단하게 이해해 보고자 한다.
```java
Runnable runner = new Runnable() {

  @Override
  public void run() {
    System.out.println("익명 클래스");
    
  }
  
};
```
여기서 `Runnable`은 인터페이스이다. 원래 인터페이스의 내용을 구현하기 위해서는 인터페이스를 implements할 클래스를 따로 만들고 코드를 구현하고 이 클래스를 생성하고 이를 인터페이스 자료형으로 받아(Up casting)사용하는 과정을 거친다.

그러나 위의 코드는 클래스를 새로 만드는 과정을 생략하고 원래는 new를 통해서 부를 수 없는 Runnable 인터페이스를 부르는데, 이는 인터페이스를 부르는게 아니라 익명 클래스를 부르고 내부 코드를 {} 코드블럭 안의 것으로 오버라이딩 하고 그 익명 객체를 Runnable runner로 Up casting 해서 받는 것이다. 
일단 이정도 이해하는 것으로 넘어가고자 한다.


### 람다식
람다식은 함수형 프로그래밍을 자바에서 구현하는 방식이다. 함수형 프로그래밍이란, 순수 함수를 구현하고 호출하는 것으로 외부 자료에 부수적인 영향을 주거나 받지 않고, 매개 변수만을 사용하도록 만든 함수이다. 파이썬이 이런 함수형 프로그래밍을 잘 활용하는 언어이다. 

람다식을 활용하면 메서드의 형식을 띄지 않고, 이름을 주지 않은 상태로 함수를 만들 수 있는데
```java
int add(int x, int y) {
  return x + y
}
// 와 같은 함수(메서드)를

(int x, int y) -> {return x + y;}
// 와 같은 형태로 사용할 수 있다.
```

람다식 문법은 다음과 같은 규칙이 있는데
- 매개변수가 하나인 경우 자료형에 괄호를 생략 가능하다.
- 그러나 매개 변수가 2개 이상인 경우 괄호를 생략할 수 없다.
- 중괄호 안의 함수 실행코드, 구현부가 한 문장인 경우 중괄호를 생략할 수 있다.
- 중괄호 안의 구현부가 1문장이더라고, return문이라면 중괄호를 생략할 수 없다.
- 중괄호 안에 구현부가 return 문 딱 하나라면, 중괄호와 return을 전부 생략할 수 있다.

#### 함수형 인터페이스 선언하기
만약 인터페이스가 추상 메서드 딱 한 개만 가지고 있다면, @FunctionalInterface 어노테이션(반드시 사용할 필요는 X)을 통해 함수형 인터페이스로 선언하고 람다 식으로 쉽게 메서드를 정의할 수 있다.

```java
@FunctionalInterface // -> 인터페이스가 메서들을 단 1개만 가지도록 강제
public interface MyNumber {
	// int getMax(int a, int b); // 둘 이상 메서드를 가질 수 없음
	int add(int a, int b); 
}
public class MyNumberTest {

	public static void main(String[] args) {
		
		// 이 인터페이스를 구현한 익명 클래스가 생성된다. 또한 그 클래스는 추상메서드를
		// 람다식으로 구현을 대체한다.
		MyNumber addfunc = (x,y) -> (x>=y) ? x : y; // addfunc는 람다식으로 재정의된 메서드를 가진 객체로 보면 된다.
		System.out.println(addfunc.add(10, 20));
  }
}
```

원래는 위의 runner 객체처럼 new 인터페이스() {내부 코드 구현} 과 같은 방식으로 기존의 인터페이스 구현을 생략했지만, 람다식을 이용하면 한층 더 줄일 수 있다. 바로 인터페이스 변수를 만들고 람다식을 입력하면 자동으로 익명 클래스가 생성되고 코드 구현부를 람다식에 구현된 것으로 채워서 리턴해준다. 더 구체적으로 설명하면, 원래 코드를 담고 있는 자료형(?)을 함수라고 한다. 그러나 자바에는 함수가 없고, 메서드를 사용할 수 밖에 없는데, 메서드를 사용하기 위해서는 클래스를 만들고 그 안에 메서드를 구현하고, 사용하기 위해서는 인스턴스를 생성해야 사용할 수 있다. 람다 식은 이 클래스 생성, 메서스 생성후 코드 작성, 인스턴스 new로 생성 하는 과정을 한 번에 자동화 하는 것이다. 그리고 이 자동화 과정에서 자동으로 만들어 지는 클래스를 익명 클래스(우리가 이름을 주지 않음)이라고 한다.

이때 주의해야할 것은 함수형 인터페이스는 반드시 메서드를 한 개만 가지고 있어야 하는데, 그 이유는 람다식으로 정의할 메서드에 혼동이 오지 않도록 하기 위해서이다.

이 과정을 한 문장으로 정리하면, **인터페이스형 변수에 람다식을 대입하여 사용**하는 것이다.
이러한 람다식은 마치 객체, 값처럼 처리할 수 있어 람다식을 반환(return)하는 것도 가능하다.

#### 자바가 제공하는 기본 Functional Interface

이 함수형 인터페이스는 자바에서 기본적으로 구현된 함수형 인터페이스이다. 사용법은 각 인터페이스에 맞는 자료형을 입력 받도록 코드를 람다식으로 작성한다. 결국 람다식으로 작성된 코드도 객체로 담겨서 인터페이스 변수에 담기기 때문에, 사용할 방법이 필요한데, 각 함수형 인터페이스 안에 1개만 구현되어 있는 함수 이름을 통해서 코드를 작동한다.

1. Consumer : 매개변수(T) -> void, 		void accept(T t) 메서드 기본 제공
2. Supplier : () -> T				T get() 메서드 기본 제공
3. Function<T,R> : T -> R 			R apply(T t) 메서드
4. Runnable : () -> void,			void run() 메서드
5. etc ...

**예시**
```java
Runnable runner = () -> System.out.println("hello");

runner.run() // "hello" 출력

Consumer adder = (a) -> System.out.println(a + "입니다.");

adder.accept(a); // "a입니다." 출력
```

### Stream
스트림(Stream)은 자료의 대상과 관계 없이 동일한 연산을 수행한다. 또한 한 번 생성한 스트림은 재사용 할 수 없다. 변수에 담을 수 없다는 것이다. 단 스트림의 결과 값은 변수에 담을 수 있다. 스트림 연산은 기존 자료형을 변경하지 않는데, 스트림을 생성하면 별도의 메모리 공간에 자료에 대한 스트림을 따로 생성한다. 마지막으로 스트림 연산은 중간 연산과 최종 연산으로 구분되고 결과 값을 리턴하는 최종 연산이 항상 마지막에 한 번 호출되어야 한다.

```java
public class StreamEx {

	public static void main(String[] args) {
		int[] nums = {1,2,3,4,5,6,7,8,9,10};
		
		int sum = Arrays.stream(nums).sum();
		long count = Arrays.stream(nums).count();
		
		System.out.println("Sum : " + sum + " Count : " + count);
		
		ArrayList<Integer> nums2 = new ArrayList<>() {
			{add(1);add(2);add(3);add(4);add(5);}
		};
		
		sum = nums2.stream().mapToInt(Integer::intValue).sum();
		count = nums2.stream().count();
		
		System.out.println("Sum : " + sum + " Count : " + count);
		
		int sum3 = Arrays.stream(nums).filter(i -> i%2==0).sum();
		long count3 = Arrays.stream(nums).filter(i -> i%2==0).count();
		
		System.out.println("Sum : " + sum3 + " Count : " + count3);
		
		Arrays.stream(nums)
			.filter(i -> i%2 == 0)
			.forEach(i -> System.out.println(i));
		
		Arrays.stream(nums)
			.filter(i -> i%2 == 0)
			.forEach(System.out::println);
		
		// 짝수를 2배해서 출력
		Arrays.stream(nums).filter(i -> i%2==0).map(i -> i*2).forEach(System.out::println);
	}

}
```

스트림에 붙을 수 있는 연산은 정말 여러 종류가 있다. 그래서 위의 코드로 스트림이 어떻게 작동하는지에 대한 흐름만 정리해보고자 한다.

다양한 연산을 짧게 정리해보면
- filter(람다 조건식) : 조건에 맞는 요소만 골라내는 중간 연산
- map(람다 연산식) : 요소를 변경해 결과를 만드는 중간 연산(각 요소에 특정 연산을 진행 *2, +1 등)
- sorted(비교 방식) : 요소를 비교해서 정렬해주는 중간연산, 만약 사용자 정의 클래스 등 요소의 정렬방식이 지정되지 않았다면, 이를 Comparator 인터페이스 등으로 정의해야한다.
- limit(숫자) : 앞에서 부터 숫자만큼 개수를 제한하는 중간 연산, 스트림에서 원하는 수만큼만 요소를 가져온다.
- distinct() : 요소의 중복을 제거하여 결과를 반환한다.
- forEach() : 요소를 하나씩 탐색하여 기능을 적용하는 최종연산이다. (Map의 역할이지만 최종 출력을 담당, reduce와의 차이는?)
- collect() : 중간 연산으로 만든 스트림 요소를 다시 배열이나 컬렉션으로 만드는 최종연산(예 : list로 반환)
- count() : 요소의 개수를 세는 최종 연산
- reduce() : **이해가 어려움, 추가 설명 필요**

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

## 4. 참고 코드 / 예시

## 5. 참고자료 / 링크

## 6. 느낀 점 / 회고 