---
layout: post
title: "7월 21일 월요일 TIL(SpringFW, DI(Dependency Injection) )"
date: 2025-07-21 18:00:00 +0900
categories: July_week4
---

# 7월 21일 월요일 TIL 작성

## 1. 학습 주제
- 의존성 주입(DI)
  - 생성자로 주입
  - 세터로 주입
- 스프링 통해서 의존성 주입
- 어노테이션을 이용한 의존성 주입


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### Spring에 객체 생성 위임

의존성 주입이란, 클래스 내에서 객체(의존성)을 직접 생성하는 것이 아니라, 외부(Spring -> Bean Container)에서 만들어진 객체(의존성)을 매개변수와 같은 형태로 주입받는 것을 의미한다.

스프링에서는 의존성을 주입하기 위해서 개발자가 코드에서 new를 통해서 객체를 생성하는 것이 아니라, 스프링이 직접 객체를 생성하고 관리한다. 이렇게 스프링이 생성한 객체를 Bean이라고 하고, 이런 Bean 들을 관리하는 곳을 Bean Container라고 한다. 스프링은 이렇게 빈 컨테이너에 있는 객체를 생성할 수 있는데, 이때 모든 객체는 **싱글톤으로** 즉 단 1개만 유지되도록 한다. 따라서 Bean으로 설정될 모든 클래스는 싱글톤으로 만들어 질 수 있도록 구성되어야 한다.

스프링에서는 개발자가 만든 클래스를 빈 콘테이너에 등록하기 위해서 XML 파일에 이를 등록한다. 기본적인 XML 형태와 bean을 등록하는 방법은 다음과 같다.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<!-- 이 부분은 그냥 복붙으로 보통 설정한다 -->
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
        
    <!-- 아래에 bean 정의  -->
    <bean id="messageService" class="app.labs.ex03.di01.MessageKo" />  <!--예시 --> 
</beans>
```
실제 bean을 등록하는 코드를 보면 `id, class` 2가지를 확인할 수 있는데, id는 컨테이너에서 빈을 사용할 때 빈을 지칭하기 위한 것이고, class는 어떤 클래스를 빈으로 지정할 것인지를 나타낸 것이다. 이때 패키지 주소 + 클래스 이름으로 작성한다.


### 의존성 주입 방법

#### 멤버 변수로 의존성 받기
기본적으로 멤버 필드로 의존성을 받을 수 있다. `IHelloService`는 인터페이스, `HelloService`는 인터페이스를 구현한 클래스(Service), `HelloController`는 service를 사용하는 controller라고 할 수 있다. 하지만 이런 방식은 강한 의존성을 가져서 추천되지 않는 방법이다.
```java
public class HelloController {
	// 이 클래스는 IHelloService에 의존성을 가진다.(강한 의존성을 가진다.)
  IHelloService service = new HelloService(); //--> 이곳을 이렇게 선언하는 것은 너무 직접적이고 고정됨. HelloService가 아닌 다른 것으로 받고 싶다면?
	
	public void hello (String name) {
		System.out.println("HelloController.hello 메서드 실행");
		System.out.println(service.sayHello(name));
	}
}
public class HelloMain {

	public static void main(String[] args) {
		
		// 1) 멤버필드
		HelloController controller = new HelloController();
    controller.hello("james");
  }
}
```

그럼 의존성을 주입해야 하는데 4가지 방법이 있다.
1. 생성자를 사용해 의존성 주입
2. Setter를 통해서 의존성 주입
3. Spring을 통해 생성자를 통해 의존성 주입
4. Spring을 통해 Setter를 통해 의존성 주입

#### 1. 생성자를 사용해 의존성 주입
1번, 생성자를 사용해 의존성을 주입하는 방식은 다음과 같다.
```java
public class HelloController {
	private IHelloService helloService;
	
	// 실제 Controller 클래스에서 객체를 만드는 것이 아닌, 만들어진 객체를 매개변수로 받아서 의존성을 주입함.
	// 이렇게 받으면 나중에 다른 것으로 바뀌어도 Controller에서는 수정할 필요 없음. 
	// 단지 실제 생성할 때 다른 것을 매개변수로 주면 된다.
	public HelloController(IHelloService helloService) { 
		this.helloService = helloService;
	}
}
public class HelloMain {

	public static void main(String[] args) {

		// 2) 생성자로 의존성 주입
		IHelloService helloService = new HelloService();
		HelloController controller = new HelloController(helloService); 
    
    controller.hello("james");
  }
}

```
이 방식은 위와 다르게 실제 `Controller` 클래스에서 `new`를 통해 객체를 만드는 것이 아닌, 만들어진 객체를 생성자의 매개 변수로 받는 방식으로 의존성을 주입한다. 이렇게 의존성을 주입할 경우 실제 다른 service 객체를 받아야 할 때, Controller의 코드를 수정하는 것이 아니라, 밖에서 다른 객체를 매개변수로 전달하는 방식으로 변경할 수 있다.


#### 2. setter를 통해서 의존성 주입
2, Setter를 통해서 의존성을 주입하는 방식은 다음과 같다.
```java
public class HelloController {
  public HelloController() {}
	
	public void setHelloService(IHelloService helloService) {
		this.helloService = helloService;
	}
	
	public void hello (String name) {
		System.out.println("HelloController.hello 메서드 실행");
		System.out.println(helloService.sayHello(name));
	}
}
public class HelloMain {

	public static void main(String[] args) {
		IHelloService helloService = new HelloService();
		HelloController controller = new HelloController();
		controller.setHelloService(helloService);
  }
}
```
Setter로 의존성을 주입하는 것은, 추후에 다시 setter를 통해서 다른 객체로 변경할 수 있다는 장점을 가진다. 그것 외에는 생성자로 입력받는 것과 대입 시기만 다르고 결과는 비슷하다. 하지만 1,2 번 방식 전부 위치만 다르고 실제 개발자가 `new`를 통해서 객체를 생성하는 것은 동일하다. 우리는 이 객체를 생성하는 부분도 프로그램에 맡겨야 한다.


#### 3. 스프링에게 생성을 맡기고 그 안에서 생성자 방식으로 의존성 주입

스프링에 객체 생성을 맡기고, 생성자 방식이나, 후에 나올 setter 방식으로 의존성을 주입하는 경우에 필요한 것이 있다.**Bean에 매개변수를 통해서 다른 것을 집어넣을 때에는 반드시 들어가는 아규먼트도 Bean이여야 한다.**

3, 3번 째 방법은 스프링에게 객체 생성을 맡기고, 이때 스프링이 bean을 생성할 때 생성자로 다른 bean을 받아서 생성하는 방법이다.
```java
public class HelloMain {

	AbstractApplicationContext context = new GenericXmlApplicationContext
				("classpath:config/di01/application-config.xml");
		
		HelloController controller = context.getBean("helloController",HelloController.class);
		
		controller.hello("james");
}
```
```XML
<!-- Service 객체 bean으로 등록 -->
<bean id="helloService" class="app.labs.ex03.di01.HelloService" />

<!-- 4. 생성자로 의존성 주입(스프링), 마치 우리가 생성자에 매개변수 넣어주 듯, 여기서도 constructor-arg를 통해서 매개변수 입력 -->
<bean id="helloController" class="app.labs.ex03.di01.HelloController">
  <constructor-arg ref="helloService" />
</bean>
```
여기서 스프링이 만든 bean을 사용하는 코드를 볼 수 있다. 사용하는 코드는 `AbstractApplicationContext context = new GenericXmlApplicationContext("classpath:config/di01/application-config.xml");`이다. 이 코드의 의미는 주소에 있는 XML 파일을 읽어오고 그 안에 등록된 클래스를 Bean으로 만들어서 context를 통해서 접근할 수 있도록 하는 것이다. 실제 HelloMain에서는 `getBean()`을 사용, id를 통해서 어떤 bean을 쓸지 지정하고 이를 받아와 사용하는 것을 볼 수 있다. 또한, `getBean()`으로 받아오는 객체는 `object`객체이기에, id로 어떤 것을 가져올지 지정하고 난 후, 클래스이름.class를 통해서 실제로 어떤 형태인지 다운캐스팅이 필요하다.

XML에 등록을 할 때에는 `<construtor-arg ref = "매개변수로 넣을 bean id">` 와 같은 방식으로 bean을 생성할 때 매개변수를 전달해 줄 수 있다. 이 방식은 Controller를 생성할 때 이 형태로 매개변수를 받는 생성자를 미리 정의해 놓아야 한다.
		

#### 4. Spring에게 생성을 맡기고, 그 안에서 setter 방식으로 의존성 주입
방식은 다음과 같다.
```java
public class HelloMain {

	AbstractApplicationContext context = new GenericXmlApplicationContext
				("classpath:config/di01/application-config.xml");
		
  // Spring setter 사용
  
  HelloController controller2 = context.getBean("helloController2",HelloController.class);
  
  controller2.hello("Bob");
  
  // setter 짧은 버전
  HelloController controller3 = context.getBean("helloController3",HelloController.class);
  
  controller2.hello("Alice");
  
  controller.hello("james");
}
```
```XML
<!-- Service 객체 bean으로 등록 -->
<bean id="helloService" class="app.labs.ex03.di01.HelloService" />

<!-- 5. setter로 의존성 주입(스프링)  -->
<!--  name 부분에는 우리가 setter 함수의 set 다음에 오는것 이름 적기 (꼭 멤버변수 이름인가?) -->
<bean id="helloController2" class="app.labs.ex03.di01.HelloController">
  <property name="helloService" ref="helloService" />
</bean>

<!-- 위의 세터를 짧게 작성하는 방법 -->
<bean id="helloController3" p:helloService-ref="helloService" class="app.labs.ex03.di01.HelloController" />
```
bean을 불러오는 방식은 위의 생성자 부분과 같다. 다른 부분은 XML에서 어떻게 정의하는가에 따라서 달라진다.

이 방식을 사용하기 위해서는 bean이 생성되기 위해서 기본생성자가 반드시 있어야 한다. 사용자가 아예 생성자를 정의해 놓지 않았으면 컴파일러에 의해서 자동으로 생성되고, 사용자 정의 생성자가 있다면 **명시적으로 기본 생성자도 생성해 주어야 한다**. 또한 이 방식을 사용하기 위해서는 **우리가 넣으려 하는 멤버 변수에 대해서 setter 또한 반드시 정의해야한다.** setter를 통한 의존성 주입은 `property`라는 키워드를 사용한다. ref는 bean으로 되어있는 우리가 set을 통해서 넣을 id, name은 우리가 정의한 setter 메서드에서 set 다음에 오는 부분을 작성한다. 이때 컴파일러는 set 다음에 name에 들어간 이름이 오는 메서드를 찾아서 그것에 ref를 넣는 방식으로 작동한다.


위의 setter로 의존성 주입을 짧게 한 줄의 코드로 하는 방법은 `p:(set뒤의name)-ref="넣을 bean의 id"`를 <bean> 사이에 넣는 것으로 할 수 있다.

### bean의 태그 속성들

위와 같은 방식을 사용할 때 XML에서는 <bean>이라는 태그를 사용해서 bean 객체를 정의한다. 그 bean 태그 안에는 다양한 속성들이 들어갈 수 있다.
- name/id : 이 속성은 bean 고유 식별자를 지정한다.
- class : **이 속성은 필수**. 빈을 작성하는데 사용할 클래스를 지정한다. 패키지 이름을 포함한 클래스 이름을 지정해야 한다.
- scope : 이 속성은 특징 빈 정의에서 생성된 객체의 범위를 지정한다. 범위 값은 `prototype, singleton, request, session` 등이 될 수 있다. 기본값은 Singleton으로 빈이 항상 한 개만 생성되도록 한다. prototype은 빈을 요청할 때 마다 매번 새로운 객체를 생성하여 반환한다. request와 session은 WebApplicationContext를 할 때 적용된다.
- lazy-init : 기본 값은 false이고 스프링은 기본적으로 해당 빈이 사용되는 시점이 아니라, 처음 시점에 모든 빈을 한 번에 전부 생성한다. true로 한다면 실제 사용되는 시점에 빈 인스턴스가 생성되도록 한다.
- init-method : 인스턴스를 생성한 후 init-method 속성에 지정한 메서드를 실행한다.
- destroy-method : 스프링 컨테이너가 빈을 삭제하기 전, destroy-method 속성에 지정한 메서드를 실행한다.


### 어노테이션을 통한 bean controller에 자동 등록

위와 같은 방법은 개발자가 클래스를 생성하면 실제로 그것을 XML 파일에 직접 등록해주어야 한다는 귀찮은 부분이 있다. 그런데 이 부분을 어노테이션으로 자동으로 처리하는 방법이 있다.

어노테이션(Annotation)의 종류는 다음과 같다.

빈을 정의하는 어노테이션
- @Component : 컨트롤러와 서비스가 아닌 그 밖의 클래스에 사용
- @Controller : 컨트롤러 클래스에 사용
- @Service : 서비스 클래스에 사용
- @Repository : 저장소 역할을 하는 클래스에 사용

다음과 같은 정의 어노테이션에는 실제 기능의 차이는 없고, 개발자가 이해하기 편하도록 지정해서 작성하는 편이다.

빈을 주입하는 어노테이션
- @Autowired : 일반적으로 사용됨. **클래스의 이름 중 제일 앞 글자르 소문자로 바꾼 id로 주입함.**
- @Qualifier : @Autowired에서 명확하게 알 수 없는 경우. 클래스 이름 앞글자 소문자로 바꾼 이름을 사용하고 싶지 않고, 더 간단하게 적고 싶거나, 이름이 겹치는 경우(드물게 발생)에서 어떤 것을 대입해야할지 명확하게 지정해 주는 방식

XML에서는 다음과 같이 작성한다.
```XML
<!-- bean을 어노테이션으로 적었으니 그것을 확인해달라고 하는 코드 -->
<context:component-scan base-package="app.labs.ex03.di03" /> <!-- 이 패키지 안에 있는 bean 어노테이션 전부 확인 -->
```

자바에서는 다음과 같이 사용한다.
```java
@Service //bean으로 등록
public class HelloService implements IHelloService {

	@Override
	public String sayHello(String name) {
		System.out.println("HelloService.sayHello() 메서드 실행");
		
		return "Hello~"+name;
	}
}

@Controller //bean 생성
public class HelloController {
	
	@Autowired //자동으로 HelloService로 만들어진 bean을 가져온다.
	IHelloService helloService; //bean인 HelloService를 받아올 수 있는 이유는 HelloController도 Bean이니까

	public void hello (String name) {
		System.out.println("HelloController.hello 메서드 실행");
		System.out.println(niceService.sayHello(name));
	}
}

public class HelloMain {

	public static void main(String[] args) {
		
		AbstractApplicationContext context = new GenericXmlApplicationContext
				("classpath:config/di03/application-config.xml");
		
		HelloController controller = context.getBean("helloController",HelloController.class); // 아이디는 클래스 앞글자만 소문자로 변경된 것으로 사용
		
		controller.hello("james");
		
		context.close();
		
	}
}
```

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### classpath: 의 의미는 무엇인가?
실제 폴더 구조는 `src/main/java`, `src/main/resources` 등 과 같이 구분되어 있는데 위에서 경로를 사용할 때는 그것에 대한 구분 없이 바로 패키지 이름부터 들어가거나, resources 내부의 폴더 이름 부터 작성하는 것에서 의문을 가졌다.

이것에 대한 대답은, 자바는 컴파일 된 이후로는 java 폴더 내부의 파일들고 resources 내부에 있는 파일들이 전부 하나의 root 폴더 아래로 새로 모인다. 그리고 이 root 폴더에 접근하는 것을 `classpath:`로 한다. 그리고 옮겨질 때 java, resources 와 같은 폴더 구조는 사라지지만, 그 안의 폴더 구조나 패키지 구분은 살아서 복사되기에 그것을 기반으로 경로를 찾을 수 있다.

## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
스프링을 본격적으로 시작한 첫 날, 확실히 이전에 servlet을 등록했을 때 보다 명확하고 편하기는 하다. 특히 어노테이션을 통해서 등록하는 것이 편리하다. 나중에 스프링 부트에서는 이것 보다 더 간단해 진다고 한다. 하지만 내가 관심이 있는 금융권은 아직도 스프링을 사용하는 경우가 많고 레거시 코드가 많아서 이것 또한 잘 이해하고 공부해 놓아야겠다!