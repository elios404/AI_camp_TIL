---
layout: post
title: "7월 28일 월요일 TIL(Spring, MVC 패턴)"
date: 2025-07-28 18:00:00 +0900
categories: July_week5
---

# 7월 28일 월요일 TIL 작성

## 1. 학습 주제

- Spring MVC 패턴
- MVC 패턴을 이용한 프로젝트 구성


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### MVC 패턴

MVC 패턴은 하나의 파일에 코드를 작성하지 않고 계층별 역할 별 에 따라서 코드를 분리해서 작성한다. 계층형 구조로  Model, View, Controller 로 구성된다. Controller 가 가운데에서 모든 것을 컨트롤한다. 요청이 오는 것을 확인하고, 처리할 모델의 정보, 출력할 view의 정보를 알고 있다.

#### 흐름
1. Web Brower -> Dispathcer Servlet : 웹 요청을 전달한다.
2. Dispathcer Servlet <-> (Spring Bean) HandlerMapping : url과 매칭되는 컨트롤러 검색을 요청하고 결과를 반환 받는다.
3. Dispathcer Servlet -> (Spring Bean) HandlerAdapter : 디스패처가 어뎁터에게 컨트롤러의 처리를 요청한다.
4. HandlerAdapter -> (Spring Bean) Controller : 실제 로직의 실행을 진행한다.
5. Controller <-(DTO)-> Service <-(DTO)-> Repository <-(DAO)-> DB : 흐름에 따라서 필요한 데이터를 요청, 메서드를 호출하고 결과를 반환한다.
6. Controller -> HandlerAdapter ->Dispathcer Servlet : 결과를 리턴하고, 컨트롤러의 실행 결과를 ModelAndView로 전환하여 Dispathcer Servlet에 리턴한다.
7. Dispathcer Servlet <-> (Spring Bean) ViewResolver : 컨트롤러의 실행 결과를 보여줄 view를 검색하고 결과 리턴
8. Dispathcer Servlet -> View -> JSP : 전달받은 데이터를 바탕으로 응답 생성을 요청하고, view에서는 이로 JSP, Thymeleaf 등의 응답 생성.

위에서 보면 **Dispathcer Servlet** 가 중심에서 모든 것을 컨트롤 하는 것을 볼 수 있다. 서블릿 등록은 @WebServlet이나 web.xml에 서블릿을 등록해야 한다. 사용할 것은 내가 만든 코드가 아니기에 web.xml에 등록하는 방식으로 진행해야한다.

### mvc02 패키지 Emp 데이터 기반 프로젝트 진행

#### 1. EmpVo.java 파일
``` java
@Getter @Setter
@ToString
@NoArgsConstructor
@AllArgsConstructor
```
다음과 같은 롬복 어노테이션을 통해서 직접 getter/setter, 생성자 등을 코딩하지 않아도 사용할 수 있고 코드를 수정하면 자동으로 수정 내용이 반영된다.

또한 멤버변수를 작성할때 명확하게 카멜케이스로 적어 주어야 하는데 이는 롬복이 정확하게 필드 값을 인식하고 자동으로 생성해 줄 수 있게 하기 위해서다.

#### 2. Repository

인터페이스에서는 어떤 기능이 필요한지 입력값과 메서드, 출력 형식을 정의한다. 이때 전체 데이터에 대한 count, 조건에 대한 count를 넣어서 데이터가 잘 불러와 지는지 확인하는 과정을 넣으면 좋다.

두 번째로 전체 직원들을 불러오는 메서드와, 각각의 직원을 불러오는 메서드를 정의했다.

또한 insert, update,delete를 진행하는 메서드를 정의했고, 마지막으로 단순히 번호로만 존재하는 managerID, jobID, deptID 등을 문자로 확인하기 위해서 다른 테이블의 정보를 가져와 실제 값을 입력할 때 보여줄 수 있게 했다.

실제 구현은 jdbcTEmplate을 이용해서 자바에서 sql문을 작성하고 db로 보내기, 결과를 받아와 리턴해 주는 것으로 구성되어 있다. 

여기서도 마찬가지로 Mapper를 통해서 행에 여러 열이 있을 때 이를 model이자 dao로 가져오도록 변환하는 클래스를 선언했다.
이때 Mapper는 dao의 set 메서드와 ResultSet rs에서 get메서드를 통해서 각각을 지정하는 방식으로 작동하는 메서드이다.

```java
// 기본적인 object 에 대해서는 sql과 클래스 타입을 지정하는 것으로 받아올 수 있다.
return jdbcTemplate.queryForObject(sql, Integer.class);

// 사용자 정의 클래스에 대해서는 Mapper를 사용하여 dao 클래스로 변환하여 받아온다.
return jdbcTemplate.query(sql, new EmpMapper());
```

#### Service

레포에 구현한 메서드를 그대로 활용, 실제 구현 클래스에서는 return 하고 repo 메서드 호출한 것을 리턴한다.

#### Controller

컨트롤러는 서비스 bean을 주입받아서 사용한다.

컨트롤러를 구현하는 과정은 1. 메서드 생성 ->  2. url 매핑 -> 3. view 생성 의 순서로 진행할 수 있다.

메서드를 생성하는 것에서 반환형을 String으로 하고 반환으로 필요한 view(JSP, HTML)의 파일 경로를 반환한다. 이때 실제 데이터는 매개변수로 Model을 받아, `model.addAttribute("호출할 이름", 데이터)`를 사용하여 model에 데이터를 넣어주고, 링크를 통해서 view를 호출하면 자동으로(?) Model의 내용이 HTML등의 view로 넘어간다.

> 추가 학습 주제
>
> Model을 통해서 어떤 과정으로 데이터가 view로 전달 되는지를 확인하기


```java
@RequestMapping(value="/hr/insert", method=RequestMethod.GET)
	public String insertEmp(Model model) {
		
		model.addAttribute("deptList",empService.getAllDeptId());
		model.addAttribute("jobList",empService.getAllJobId());
		model.addAttribute("managerList",empService.getAllManagerId());
		
		return "hr/insertform";
	}
```
ReqeustMapping을 통해서 url을 메서드와 매핑할 수 있고, method를 통해서 HTTP의 어떤 메서드를 사용할 것인지 지정할 수 있다.

> 추가 학습 주제
>
> @ReqeustMapping과 추후에 배울 @GetMapping @PostMapping은 어떤 차이가 있는가?
> 스프링 프로젝트를 사용할 때는 @ReqeustMapping을 사용했고, 스프링 부트에서는 @GetMapping을 주로 사용하였지만, 이는 스프링과 부트의 차이는 아니고 스프링 버전에 차이이다. 스프링 4.3 버전 이후로 @GetMapping과 같은 방식으로 사용 가능하다.
> @ReqeustMapping 는 기본적으로 모든 메서드 방식의 요청을 받을 수 있고, `method=`를 통해서 특정 메서드를 받도록 지정한다.
> 반면 @GetMapping 은 특정 메서드를 초기에 지정해서 받을 수 있게 한다.


|특징 | @RequestMapping	 | @GetMapping (등)|
|---|---|---|
|HTTP 메소드 | 모든 HTTP 메소드 (GET, POST, PUT, DELETE 등) 처리 가능 |	특정 HTTP 메소드에 고정 (예: @GetMapping은 GET만) |
|명시성	 | method 속성으로 명시해야 함 (생략 시 모든 메소드) | 어노테이션 이름 자체가 메소드를 명시 |
|간결성	 | 더 길고 많은 속성을 가질 수 있음 | 간결하고 직관적임 |
| 가독성 |	메소드까지 확인해야 함 | 어노테이션 이름만으로 역할 파악 가능 |
| 등장 시기 |	스프링 초기부터 존재 | 스프링 4.3부터 도입|
| 사용 목적	| 더 유연한 매핑, 다목적 |	특정 HTTP 메소드 매핑에 최적화 |

#### view

Model을 통해서 전달된 데이터는 jsp, HTML에서 `${호출할 이름}`으로 사용할 수 있다. 구체적인 사용 방식이나, 반복문, 조건문 사용은 springFw 프로젝트의  src/main/webapp/views 폴더 안의 파일들안에 들어 있다. (추후에 학습할 때 이 부분 참조) -> 이후에 배우는 thymeleaf를 사용하여 같은 것을 HTML 파일로 사용할 수 있다.


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
