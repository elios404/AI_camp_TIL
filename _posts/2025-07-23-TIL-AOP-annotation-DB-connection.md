---
layout: post
title: "7월 23일 수요일 TIL(Spring AOP, Oracle DB)"
date: 2025-07-23 18:00:00 +0900
categories: July_week4
---

# 7월 23일 수요일 TIL 작성

## 1. 학습 주제
- AOP
  - Around
  - AOP(Annotation)
- DB


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
AOP(스프링에서)을 통해서 다른 사람의 코드를 건드리지 않고, 자신이 원하는 기능을 특정 메서드의 앞, 뒤, 리턴 시 등 여러 타이밍에 추가할 수 있다. 이를 이용하여 모니터링 시스템, 보안 시스템 등 기존의 기능을 건드리지 않고, 추가해야하는 기능이 있을 때 사용할 수 있다.

**(Java)Bean의 관례**
1. 모든 멤버 변수는 private으로
2. get/set은 무조건 생성
3. 기본 생성자가 있어야 한다.

하지만 우리가 이제껏 빈으로 만든 것은 그것을 지키지 않았다. 엄격한 기준에서는 bean이라고 할 수 없다. 따라서 우리가 만든 것은 Spring bean이라는 표현을 사용하는 것이 더 정확하다.


### Around advice
AOP에서 다른 advice와는 다르게, around는 메서드의 앞 뒤 중간에 끼어들기에 구현하는 방식이 약간 다르다. 이때는 추가로 클래스와 메서드를 구현하고 우리가 선택한 pointcut 메서드를 직접 매개변수로 받아서 가져와야한다. 직접적인 코드는 다음과 같다.
```XML
<!-- Target Object : 핵심 관심사 -->
    <bean id = "helloService" class = "app.labs.ex04.aop01.HelloService"/> 
    <bean id = "helloController" class = "app.labs.ex04.aop01.HelloController">
    	<property name="helloService" ref="helloService" />
    </bean>
    
    <!-- Aspect : 공통 관심사 -->
    <bean id = "helloLog" class = "app.labs.ex04.aop01.HelloLog"/> 
    <bean id = "performanceAspect" class = "app.labs.ex04.aop01.PerformanceAspect"/> <!-- around 할 것을 빈으로 등록 -->
    
    <!-- AOP 설정 : 공통 관심사를 핵심 관심사(메서드) 기준으로 실행 시점을 지정-->
    <aop:config>
    	<!-- 핵심 관심사(객체) 중 어떤 메서드에 적용할지 선택 : PointCut -->
    	<aop:pointcut expression="execution(* app.labs.ex04..HelloService.sayHello(..))" id="helloPointCut"/>

    	<aop:aspect ref="performanceAspect">
    		<aop:around method="trace" pointcut-ref = "helloPointCut"/> <!-- 어떤 메서드로 around 실행할지, 어떤 메서드를 매개변수로 넣을지 -->
    	</aop:aspect>
    </aop:config>
```
```java
public class PerformanceAspect { 
	
	public Object trace(ProceedingJoinPoint joinPoint) throws Throwable { //발생하는 모든 에러를 상위로 던짐, -> 받는 것은 누구?
		
		Signature s = joinPoint.getSignature(); // 현재의 Pointcut들의 시그니처를 가져오기
		
		String methodName = s.getName(); //그것이 어떤 메서드인지 이름 확인
		System.out.println("[Log] Before: " + methodName + " time check starts");
		
		Long startTime = System.nanoTime();
		
		Object result = null;
		
		try {
			result = joinPoint.proceed(); // 기존의 핵심 관심 메서드의 리턴값을 받기
		} catch(Exception ex) {
			System.out.println("[Log] Exception : " + methodName);
		} finally {
			System.out.println("[Log] Finally : " + methodName);
		}
		
		Long endTime = System.nanoTime();
		
		System.out.println("[Log] After: " + methodName + " time check ends");
		System.out.println("[Log] " + methodName + ":" + (endTime-startTime) +"ns");
		
		return result; // 우리가 pointcut 메서드를 현재 실행 메서드로 인터셉트 했기 때문에 결과 값을 대신 전달
		
	}
}
```

우리가 pointcut으로 설정하는 메서드는, 그 클래스가 빈 컨테이너에 등록되어 빈으로 생성되는 클래스여야 한다.  Around로 사용하기 위해서 정의한 메서드를 담은 클래스를 빈으로 정의한 다음 그것을 <aop>에서 불러온다. 

내부 코드를 보면 우리가 around할 실제 핵심 기능 메서드를 매개변수로 받는다. 또한 Signature를 통해서 그 메서드의 이름 등 정보를 얻어올 수 있다. 중간에 `proceed()`를 통해서 핵심 기능 메서드를 실행하고, 우리는 지금 그 메서드를 다른 메서드 안으로 끌고 들어와서 실행시켜 주었기에 메서드의 반환값을 밖으로 내보내 주기 위해서 전체 객체를 담을 수 있는 `Object result`를 통해서 객체를 밖으로 리턴한다.


### @(annotation)을 이용한 AOP 구현

```java
@Component
@Aspect // AOP를 구현하기 위한 어노테이션
public class LogAspect {
	
	// Pointcut(핵심 코드)
	@Pointcut(value = "execution(* app.labs.ex04..HelloService.sayHello(..))") 
	private void helloPointcut() {} //메서드 이름이 아이디가 됨, 핵심 관심 기능을 여기에
	
	@Pointcut(value = "execution(* app.labs.ex04..HelloService.sayGoodbye(..))") 
	private void goodbyePointcut() {} //메서드 이름이 아이디가 됨, 핵심 관심 기능을 여기에
	
	// Advice(공통코드 + 시점) - 메서드 이름이 id가 된다.
	@Before("helloPointcut()")
	public void beforeLog(JoinPoint joinPoint) { // Aspect(공통 코드)
		System.out.println(">>> Log:" + new java.util.Date());
	}
	
	@AfterReturning(pointcut="goodbyePointcut()", returning="message") //goodbye 메서드가 문자열을 리턴한 뒤, 리턴 값을 returnig에 담아오기
    public void afterLog(JoinPoint joinPoint, Object message) { // Aspect
        System.out.println(">>> Log:" + new java.util.Date() + " ++ message : " + message.toString());
    }
	
	@Around("helloPointcut() || goodbyePointcut()") // 이곳에서 매서드 이름 ()까지 포함해서 빈의 아이디를 사용한다. || 를 통해서 여러 ID를 사용할 수 있다.
	public Object trace(ProceedingJoinPoint joinPoint) throws Throwable { 
		
		Signature s = joinPoint.getSignature(); 
		
		String methodName = s.getName(); 
		System.out.println("[Log] Before: " + methodName + " time check starts");
		
		Long startTime = System.nanoTime();
		
		Object result = null;
		
		try {
			result = joinPoint.proceed(); 
		} catch(Exception ex) {
			System.out.println("[Log] Exception : " + methodName);
		} finally {
			System.out.println("[Log] Finally : " + methodName);
		}
		
		Long endTime = System.nanoTime();
		
		System.out.println("[Log] After: " + methodName + " time check ends");
		System.out.println("[Log] " + methodName + ":" + (endTime-startTime) +"ns");
		
		return result; 
	}
}
```

`@Component` 어노테이션을 통해서 우리가 만든 Aspect 설정 파일을 빈으로 만들어서 빈 컨테이너에 올려야 한다. 또한 이때 `@Aspect`를 통해서 이것이 AOP 설정 파일이라는 것을 나타낸다.
`@Pointcut(value = "execution(* app.labs.ex04..HelloService.sayHello(..))") ` 와 같은 표현식을 통해서 실제 어떤 패키지의 클래스 안에 있는 메서드를 Pointcut으로 등록할 것인지를 나타내고 `private void 메서드이름()` 을 통해서 "메서드 이름()" 이 형태로 메서드의 ID를 생성한다. 또한 Advice를 `@`어노테이션으로 선언하고 기본적으로 Pointcut으로 지정할 메서드 아이디를 안에 입력한다. 

### DB

데이터 베이스는 기본적으로 **불변형**인 데이터를 저장하기 위해 사용한다. 수정은 최소화 하고, 삭제는 매우 엄격하게 설정하며, 새로운 데이터의 입력과 데이터의 읽기는 자유롭게 가능하다.

역사를 조금 살펴 보면 예전에는 파일을 통해서 데이터를 저장했다. 그리고 여러 사람이 같은 데이터를 통해서 협업하기 위해서 개인 PC에 있는 파일을 공유할 수 있는 서버가 등장했다. 서버를 통해서 파일을 공유했지만, 여전히 한 사람이 파일을 열고 있을 때, 락이 걸려 다른 사람은 파일에 접근할 수 없는 문제가 있었다. 이후 등장한 것은 데이터는 파일로 저장하지만, 그것을 실제 사용하고 수정할 때에는 파일에 있는 데이터를 **메모리에 올리는 것**으로 같은 파일에 있는 데이터라도 여러 명이 각각 사용할 수 있도록 개선되었다.

SQL문은 이렇게 파일에 있는 데이터를 메모리에 올리는 작업을 명령하는 역할과 함께 여러 기능을 가진다.
- 데이터 정의어(DDL)을 통해서 데이터베이스의 구조(테이블, 인덱스 등)를 정의한다.
- 데이터 조작어(DML)를 통해 데이터를 검색(SELECT), 삽입(INSERT), 수정(UPDATE), 삭제(DELETE)하는 작업을 명령한다.
- 데이터 제어어(DCL)를 통해 사용자 권한을 제어한다.

SQL 쿼리를 실행하면, DBMS는 SQL문을 파싱하고, 최적화하여, **필요한 데이터를 디스크에서 메모리로 로드하고, 메모리에서 해당 작업을 수행한 후, 필요한 경우 다시 디스크에 반영하는 일련의 복잡한 과정**을 내부적으로 처리합니다. 

DB의 데이터를 자바에서 활용하기 위해서는 DB서버 메모리에 있는 데이터를 자바 JVM의 메모리로 가져와야 한다. 이때 역할을 하는 것이 JDBC이다. DB메모리에서 자바 메모리로 데이터를 가져오는 역할을 한다. 또한 사용자가 자바에서 SQL문을 올리면 그것을 DB로 보내는 역할도 한다.

과정은 DB선택(Driver) → DB 연결(Connection) → SQL 전송(Statement) → 변수로 결과 받기(Result Set) 로 이루어 진다. 

또한 JDBC는 DB와 JVM에서 있는 자료형의 차이를 타입 매칭을 해준다.

- JDBC Driver API : 데이터 베이스 연결과 관련된 기능 제공, DB 선택하고 연결하고
- JDBC API : 자바 앱에서 SQL 실행에 관련된 기능 제공, 보내고 받기

DB의 **종류**를 특정하고 연결을 해야할 때 

- 위치
- 데이터베이스(특정한 데이터베이스)
- 계정

3가지가 필요하다. (종류 까지 포함하면 4개)

이렇게 연결을 완료하면 SQL문을 보낸다. → DB에서는 받은 SQL을 실행하여 메모리에 올린다. 그 뒤 위에서 연결된 대로 데이터를 자바 메모리로 보내준다.

이렇게 데이터를 받은 뒤 데이터베이스를 다른 사람도 사용하고 위해서 연결을 끊는다.

하지만 매 SQL문을 사용할 때 마다 연결을 만들고 끊는 것은 시간 소모가 들 수 있음. 따라서 미리 안정된, 검증된 연결 여러개를 만들어 놓고, 사용할 때 빌려주고 되돌려 받는(단 연결을 끊지는 않음) 방식으로 하기도 한다. `PooledConnection` 방식

→ 이 때 미리 만들어 놓은 연결의 수는 제한이 있다. 그래서 많은 수의 요청이 나오더라도, 한 번에 리턴을 받을 수 있는 것은 연결의 수만큼만 가능하다. **DB에서 병목 현상이 생긴다** . 따라서 DB에서 속도를 높이고 SQL 문을 짧게, 최적화 하는 것이 중요하다.


### Spring JDBC (개요 부분 pdf 확인)

Java JDBC를 사용하면 DB 연결과 SQL문 전달, 데이터 가져오기가 너무 불편하다. 

→ Spring JDBC : 연결하는 하고 전달하고 받는 부분을 자동화 함. 그러나 여전히 SQL문이 java 파일 안에 있음

→ MyBatis : SQL문을 따로 분리해서 설정파일에 작성


### transaction

all or nothing : 양 쪽이 성공할 때만 commit, 아니면 rollback

예를 들어, 버스 카드로 계산할 때 내 돈은 나갔는데, 버스는 돈 + 가 안되어서 다시 내는 상황을 막음. 한쪽이 돈 + 가 안되면 다른 쪽 - 도 rollback.

양쪽이 다 완료되어야, 그 결과가 commit 됨.

자바를 통해서 데이터베이스를 연결하고, sql을 보내고 데이터를 읽어오는 과정

→ jdbc폴더와, jdbc01 폴더에서 확인 가능

### 롬복

멤버 변수가 많을 때 get/set을 전부 하면 너무 길어져서? → 이런 것을 자동으로 해주는 라이브러리


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
