---
layout: post
title: "7월 29일 화요일 TIL(Spring MVC-2, MyBatis)"
date: 2025-07-29 18:00:00 +0900
categories: July_week5
---

# 7월 29일 화요일 TIL 작성

## 1. 학습 주제
- Spring MVC
- Get/Post HTTP 방식
- 모델&뷰

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

기술을 사용할 때, 왜 그 기술을 넣었는지를 항상 생각해봐라.

`~를 해결하기 위해서` 이것이 도메인이 될 수 있다. → 관심 있는 도메인을 항상 고민해보고 어떤 문제가 있을지 생각해 봐야한다. 그 분야에 어떤 문제점, 불편한 점이 있는지 확인하고, 그것을 해결할 수 있는 방법이 무엇이 있을까 생각하자.

### dao/dto의 차이

#### DAO (Data Access Object)
개념: DAO는 데이터베이스에 접근하는 로직을 캡슐화(추상화)한 객체이다. 즉, 데이터베이스 CRUD(Create, Read, Update, Delete) 작업을 수행하는 데 필요한 기능들을 모아놓은 객체라고 볼 수 있다. 애플리케이션의 비즈니스 로직과 데이터 접근 로직을 분리하여, 코드의 재사용성을 높이고 유지보수를 용이하게 만든다.

왜 사용할까?:

관심사 분리 (Separation of Concerns): 비즈니스 로직(예: 회원가입, 상품 주문)과 데이터베이스 접근 로직을 분리하여 각 계층이 자신의 역할에만 집중하도록 한다.

유지보수성 향상: 데이터베이스가 변경되거나 접근 방식이 달라져도 비즈니스 로직은 크게 변경하지 않아도 됩니다. DAO 내부만 수정하면 된다.

재사용성: 데이터베이스 연동 코드를 여러 곳에서 재사용할 수 있다.

#### DTO (Data Transfer Object)
개념: DTO는 계층 간 데이터 교환을 위해 사용하는 객체이다. 데이터를 담아서 전송하는 용도로만 사용되며, 비즈니스 로직을 포함하지 않고 순수하게 데이터만 담는 역할을 한다. 주로 데이터베이스에서 가져온 데이터를 담거나, 사용자로부터 입력받은 데이터를 담아 다른 계층(예: 웹 계층 -> 서비스 계층)으로 전달할 때 주로 사용한다.

왜 사용할까?:

데이터 캡슐화: 여러 필드를 하나의 객체로 묶어 데이터를 효율적으로 전송할 수 있다.

계층 간 의존성 감소: 특정 계층의 객체를 직접 주고받는 대신 DTO를 통해 데이터를 전달함으로써 계층 간의 결합도를 낮춘다.

보안 및 불필요한 정보 노출 방지: 모든 데이터를 넘길 필요 없이 필요한 정보만 담아 전달할 수 있다.

### get/post 방식의 차이점(간단하게)

디폴트 설정은 get 방식이다. http에서는 method: get/post 등을 선택, header, body 부분 등이 있다.

- get : url을 통해서 보내는 것(결과를?, 매개변수를?, 호출을?)이다. 관련 정보를 url에 담아서 보낸다.
- post : post 방식은 관련 정보를 body에 넣어서 보낸다.

[`http://localhost:8088/springFw/hr/count?deptid=100`] 라는 url이 있을 때 우리는 deptid=100으로 정보를 get 방식으로 전달한다. url로 확인할 수 있다.

그리고 결과인 사원 수 : 6 이라는 출력에서 6은 response를 서버가 보낼 때 응답의 body에 담겨서 온다. 



### json으로 데이터를 보내는 방법 (RESTful API 맛보기)

```java
@RequestMapping(value="/hr/json")
public @ResponseBody List<EmpVO> getEmpJSONList() { //이게 뭐하는 용도? view가 없기에 사용? Ajax을 위해서?
    
    return empService.getEmpList();
}


@RequestMapping(value="/hr/json/{employeeId}") //이걸로 호출이 들오오면
public @ResponseBody EmpVO getEmpJSONInfo(@PathVariable int employeeId, Model model) { // 매개변수를 경로 주소에 사용

    return empService.getEmpInfo(employeeId); //이것을 리턴하기
}
```
`@RequestMapping(value="/hr/json")` 이 어노테이션은 /hr/json 경로로 들어오는 HTTP 요청을 이 메서드가 처리하겠다고 매핑하는 역할을 한다. 별도의 method 속성이 없으므로 기본적으로 모든 HTTP 메서드(GET, POST 등)에 반응하지만, 일반적으로 데이터를 조회하는 용도라면 GET 요청으로 사용된다.

#### public @ResponseBody List<EmpVO> getEmpJSONList()

`@ResponseBody`: 이 어노테이션이 핵심부분이다. 메서드의 반환 값(여기서는 List<EmpVO>)이 HTTP 응답 본문(Body)으로 직접 전송되어야 함을 Spring에게 알려줍니다. 즉, 이 메서드가 View(JSP, Thymeleaf 등)를 찾아서 렌더링하는 대신, 반환되는 객체를 적절한 형식(여기서는 JSON)으로 변환하여 클라이언트에게 바로 보낸다는 의미를 나타낸다.

`List<EmpVO>`: EmpVO는 Emp(Employee: 사원) 정보를 담는 Value Object (VO) 혹은 **Data Transfer Object (DTO)**라고 할 수 있다. 이 메서드는 여러 사원의 정보를 리스트 형태로 반환한다. RESTful은 보통 Map<String, Object> 로 반환하여 그것의 key(문자열로 데이터가 무엇인지), 와 실제 데이터로 구성한다.

`return empService.getEmpList()`;: empService는 사원 관련 비즈니스 로직을 처리하는 서비스 계층의 객체이며, getEmpList() 메서드를 호출하여 모든 사원 목록을 가져와 반환한다.

#### 무엇을 위해서 하는 것일까?

@ResponseBody를 사용한다는 것은 View(화면)를 렌더링하지 않고 데이터 자체를 응답으로 반환하겠다는 것이다.

주로 AJAX 통신을 위한 용도로 사용된다. 웹 브라우저의 JavaScript 코드(jQuery의 $.ajax(), fetch API 등)가 이 URL로 요청을 보내고, 서버는 JSON 데이터를 응답으로 보낸다. 클라이언트(브라우저)는 이 JSON 데이터를 받아서 JavaScript로 동적으로 웹 페이지의 일부를 업데이트하거나 다른 처리를 수행할 수 있다.

모바일 앱(안드로이드, iOS)이나 다른 백엔드 서비스 등, HTML 페이지가 아닌 순수한 데이터를 주고받아야 하는 경우에 RESTful API 엔드포인트로 활용된다.


### 스프링 에러 처리를 할 때 xml을 통해서 하는게 좋다. 각 에러에 대한 페이지로 리턴되도록

스프링 MVC 애플리케이션에서 **발생하는 다양한 오류(에러)들을 XML 설정 파일을 통해 중앙에서 관리하고, 특정 오류가 발생했을 때 사용자에게 보여줄 전용 에러 페이지로 자동으로 이동시키겠다**는 의미인 것 같다.

이는 스프링의 SimpleMappingExceptionResolver나 커스텀 에러 핸들러 등을 XML에 설정하여 구현한다.

#### 왜 XML을 통해 에러 처리를 설정할까?
과거 스프링 버전(특히 스프링 3.x, 4.x 시절)에서는 XML 설정이 일반적이었다.(여러 설정 파일들을 xml로 작성) 요즘은 주로 자바 기반의 설정(@Configuration 클래스)을 사용하지만, XML을 통한 설정도 여전히 유효하며 다음과 같은 이점이 있다.

- 중앙 집중식 관리: 모든 에러 처리 로직을 하나의 XML 파일(예: servlet-context.xml 또는 별도의 exception-handler.xml)에 모아두면, 애플리케이션 전체의 에러 처리 정책을 한눈에 파악하고 관리하기 용이합니다.

- 관심사 분리: 비즈니스 로직을 담고 있는 컨트롤러 코드에서 직접적인 에러 처리 로직을 분리하여, 컨트롤러는 본연의 역할(요청 처리)에만 집중할 수 있게 합니다.

- 유연성: 특정 예외 클래스에 대해 특정 에러 페이지를 매핑하거나, 기본 에러 페이지를 설정하는 등 다양한 방식으로 유연하게 에러 처리를 구성할 수 있습니다.

- 유지보수 용이: 에러 처리 정책이 변경될 때, XML 파일만 수정하면 되므로 코드 재컴파일 없이 적용이 가능하거나, 변경 범위가 최소화됩니다.

#### "각 에러에 대한 페이지로 리턴되도록"의 의미와 구현 방식
이는 사용자 경험 측면에서 매우 중요합니다. 서버 에러가 발생했을 때 백엔드의 복잡한 오류 메시지를 그대로 사용자에게 보여주는 것은 좋지 않다. 대신, 사용자에게 친숙하고 어떤 문제가 발생했는지 알려주는 전용 에러 페이지를 보여주는 것이 일반적이고 사용자 친화적이다.

예를 들어, 다음과 같은 상황을 처리할 수 있다.

- 404 Not Found (페이지를 찾을 수 없음): 사용자가 잘못된 URL로 접근했을 때 "요청하신 페이지를 찾을 수 없습니다."와 같은 메시지를 담은 404.jsp 또는 404.html 페이지를 보여줍니다.

- 500 Internal Server Error (서버 내부 오류): 개발자가 예상하지 못한 예외가 발생했을 때 "죄송합니다. 서비스 이용에 불편을 드려 죄송합니다."와 같은 메시지를 담은 500.jsp 페이지를 보여줍니다.

- 특정 예외 (CustomException): 애플리케이션에서 정의한 특정 비즈니스 로직 예외(예: NotEnoughStockException, UserNotFoundException)가 발생했을 때, 해당 예외에 맞는 상세한 메시지를 보여주는 페이지로 이동시킬 수 있습니다.


### 인터셉터

인터셉터 민들기 내용(PageViewLogInterceptor.java) → 로그인 기능과 관련된 곳에 사용할 수 있다.

로그인 관련은 preHandle에서 보통 많이 한다. 로그인이 필요한 url로 접근할 떄

```java
package app.labs.ex06.mvc02;

import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

public class PageViewLogInterceptor implements HandlerInterceptor {
	
	// 컨트롤러로 넘어가기 전에
	@Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        // TODO Auto-generated method stub
        request.setCharacterEncoding("utf-8");

        // false 인터셉터로 제어가 넘어감, 컨트롤러로 넘어가지 않음
        return true; //true 면 그대로 컨트롤러로 제어가 넘어감
    }

	// 컨트롤러에서 뷰로 넘어가기 전에
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
            ModelAndView modelAndView) throws Exception {
        // TODO Auto-generated method stub
        System.out.println("컨트롤러 실행됨.");

    }
    
    // 뷰를 작동한 후에
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
        // TODO Auto-generated method stub
        System.out.println("뷰가 처리됨.");

    }
}
```

이 코드에서 인터셉터의 기능을 직접적으로 작동하는 코드는 바로 @Override된 세 가지 메서드들이다.

- preHandle(): 컨트롤러 실행 전에 호출

- postHandle(): 컨트롤러 실행 후, 뷰 렌더링 전에 호출

- afterCompletion(): 뷰 렌더링 완료 후에 호출

각 메서드 안에 작성된 로직이 바로 이 인터셉터가 수행하는 "기능"이다.


`제어 흐름 제어: return true;` preHandle 메서드가 true를 반환하면 다음 인터셉터나 컨트롤러로 요청 처리가 계속 진행됩니다. 만약 false를 반환하면 요청 처리가 중단되고 컨트롤러로 넘어가지 않는다.

PageViewLogInterceptor 클래스는 **Spring의 HandlerInterceptor 인터페이스를 구현하여 웹 요청 처리 과정의 특정 지점들(컨트롤러 실행 전/후, 뷰 처리 완료 후)에서 코드를 삽입하고 실행하는 기능을 가지고 있습니다.**

### MyBatis

현재 자바의  repository.java에 묶여있는 sql문을 따로 설정 파일로 분리해서 활용할 수 있게 한다. 기존에 Repository에서 작성한 SQL 문을 담고 있는 클래스를 만들지 않고, 인터페이스와 메서드만 정의해 놓은 뒤 Mapper 를 만들어서 SQL을 담당하는 별개의 파일을 만드는 방식으로 구현한다.


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
