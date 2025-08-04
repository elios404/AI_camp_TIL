---
layout: post
title: "8월 1일 금요일 TIL(Spring Boot, HTML , Java Script)"
date: 2025-08-01 18:00:00 +0900
categories: July_week5
---

# 8월 1일 금요일 TIL 작성

## 1. 학습 주제

- Slf4j
- HTML
  - bootstrap
  - fragment
  - action
- Java Script


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

#### Slf4j

*@Slf4j* //로그를 위한 인터페이스 역할, logback과 log4j2 를 설정을 통해 설정하고, 사용할 땐 slf4j를 통해서 사용 가능하게 한다. 어떤 로그 프로그램을 사용할지 각 파일에 지정해 주는 것이 아니라, 설정 파일에 설정하고, 사용자는 Slf4j를 사용하도록, 로그프로그램을 캡슐화한 것, 인터페이스 화, PSA , 서비스 추상화

bootstrap 통해서 디자인 하는 것 list.html 학습함 + 이곳에서 데이터 삽입, 수정, 삭제 전부 진행, controller에 메서드 추가하고, list.html에서 이를 진행할 UI 만들기

fragement : html을 조각내어서 각 부분을 따로 구현하는 것?


**list.html 을 오늘 수업에서 대부분 진행함. 이 부분을 정리하면 될 듯!**

### 자바 스크립트

브라우져가 html 태그 등을 통해서 만든 **요소객체**를 컨트롤 할 수 있다.

현재 프로젝트의 메모리는 크게 3개, DB, 자바 JVM, 브라우져의 메모리 이렇게 3개가 있다.

DB는 SQL문을 통해서 메모리에 올림. 자바는 new를 통해서 메모리에 올림.  브라우져는 tag를 이용해서 메모리에 올린다. DB와 자바 사이에서는 JDBC를 이용해서 메모리에 있는 내용을 공유, 자바와 브라우져 사이에서는 HTML을 이용해서 메모리에 있는 내용을 공유한다. 

자바 스크립트는 브라우져의 메모리에 올라와 있는 데이터, 요소 객체를 핸들링 하는데 사용한다.

브라우져에서 실제 출력 화면은 document가 관리, 주소창은 브라우져의 location에서 관리

자바스크립트와 브라우져 이벤트에 대한 것에 대해 학습함.

submit 버튼은 form 안에 있어서, form에 있는 내용을 action에 전달하라는 명령, 이벤트

event가 발생하면(조건) → callback function를 호출 혹은 event handler 를 실행

#### 여러개의 버튼을 구분

1. submit 버튼이 클릭됨, submit이 눌렸는지 확인하는 조건, 이벤트가 발생하면
2. 버튼 id를 체크하고,
3. id에 맞는 해당 action을 지정

이것을 자바 스크립트를 통해서 구현

submit 이벤트는 클릭과 서버에 보내기가 같이 발생하는 이벤트

→ mouseover/mouseenter ,  mouseout/mouseleave 의 차이 : 자식 등에게 전파가 되는가 안 되는가

자바 스크립트 객체 표기법 {  property(+get/set) : return value } 와 같은 방식을 이용, list.html 참조


### Spring boot dept 프로젝트 내용

#### Dept.java(model)
```java
@Getter @Setter
@ToString
@NoArgsConstructor
@AllArgsConstructor
@Builder
```
와 같은 어노테이션을 통해서 별 다른 추가 코드 없이, 멤벼 변수, 정확히는 DB에 있는 데이터 컬럼을 private로 정의한다.

#### DeptRepository.java(Repository)

```java
@Mapper
public interface DeptRepository {
	
	int getDeptCount(); //카운트는 무조건 만든다.
    int getDeptCount(@Param("deptno") int deptno);
    
    List<Dept> getDeptList(); //전체 리스트 가져오기
    Dept getDeptInfo(int deptno); // 각각의 행 가져오기 -> 1개의 행만 가져와야 하는 것은 고유키를 사용
    
    // db 삽입 수정 삭제
    void insertDept(Dept dept);
    void updateDept(Dept dept);
    int deleteDept(@Param("deptno") int deptno); // -> 1개의 행만 가져와야 하는 것은 고유키를 사용
}
```

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="app.labs.dept.dao.DeptRepository">

	<select id="getDeptCount" parameterType="int" resultType="int">
		SELECT COUNT(*) FROM DEPT
		<if test="deptno != null">
			 WHERE DEPTNO = #{deptno}
		</if>
	</select>
	
	<select id="getDeptList" resultType="app.labs.dept.model.Dept">
		SELECT * FROM DEPT
	</select>
	
	<select id="getDeptInfo" parameterType="int" resultType="app.labs.dept.model.Dept">
		SELECT 	DEPTNO 	AS 	deptNo,
				DNAME 	AS 	dName,
				LOC 	AS 	loc
		FROM DEPT WHERE DEPTNO = #{deptno}
	</select>

	<update id="updateDept" parameterType="app.labs.dept.model.Dept">
		UPDATE DEPT 
		SET DNAME=#{dName}, LOC=#{loc}
		WHERE DEPTNO = #{deptNo}
	</update>
	
	<insert id="insertDept" parameterType="app.labs.dept.model.Dept">
		INSERT INTO DEPT ( DEPTNO, DNAME, LOC )
		VALUES ( #{deptNo}, #{dName}, #{loc} )
	</insert>
	
	<delete id="deleteDept" parameterType="app.labs.dept.model.Dept">
		DELETE FROM DEPT 
		WHERE DEPTNO=#{deptno}
	</delete>
</mapper>
```
위에서 구현된 인터페이스를 resource 폴더에 따로 Mapper 패키지를 만들고 인터페이스를 구현한 메퍼 파일을 생성한다. 그 후 SQL을 작성한다.

인터페이스로 repository를 구현하고 필요한 메서드를 정의한다. 기본적으로 필요한 메서드들을 작성해 놓았다.

#### DeptService.java(interface), BasicDeptService.java(implement)

프로젝트의 구현 방향에 따라 다르겠지만, 기본적으로 repositoy에서 정의한 기능을 그대로 사용한다. 나중에 기능이 복잡해 지면, DB에서 데이터를 가져오거나 수정하는 비지니스 로직 뿐만 아니라, 다른 로직도 이곳에 작성한다. 현재로써는 매개변수를 통해서 Repository에 있는 메서드를 호출하는 것으로 구현되어 있다.

#### DeptController.java(Controller)


**@RequestParam과 객체(DTO/VO) 파라미터 바인딩 요약**

Spring MVC에서 HTTP 요청의 파라미터를 컨트롤러 메서드의 인자로 받을 때, @RequestParam 사용 여부는 해당 인자의 타입에 따라 달라진다.

- 원시(Primitive) 타입 또는 단일 값 타입 (int, String 등): 이 경우 Spring은 어떤 HTTP 파라미터가 해당 인자에 매핑되어야 하는지 알 수 없으므로, @RequestParam 어노테이션을 명시적으로 사용해야 한다. 이는 요청에서 deptno=100과 같이 특정 이름의 값을 가져와 int deptNo 변수에 할당하라고 지시하는 역할이다. required와 defaultValue 속성을 통해 해당 파라미터가 필수가 아닌 경우나 기본값을 지정하는 유연성을 제공한다.

- 객체(DTO/VO) 타입 (Dept dept 등): 이 경우 Spring은 HTTP 요청의 쿼리 파라미터(GET 요청)나 폼 데이터(POST 요청)를 해당 객체의 내부 필드와 자동으로 매핑해준다. 즉, 요청 파라미터 이름(예: deptNo, deptName)과 객체의 필드 이름이 일치하면, Spring이 자동으로 값을 찾아 객체를 생성하고 채워서 메서드 인자로 넘겨준다. 따라서 @RequestParam을 별도로 명시할 필요가 없고, 이는 GET 요청이든 POST 요청이든 동일하게 적용된다.

결론적으로, int와 같은 단일 값은 @RequestParam으로 명시하고, DTO/VO와 같은 복합 객체는 Spring의 자동 바인딩 기능을 활용하여 편리하게 데이터를 전달받을 수 있다.


```java
package app.labs.dept.controller;

// import 구문 생략

@Slf4j //로그를 위한 인터페이스 역할, logback과 log4j2 를 설정을 통해 설정하고, 사용할 땐 slf4j만 사용
@Controller
@RequestMapping("dept") //그렇다기 보다는 가장 상위 url이라고 볼 수 있다.
public class DeptController {
	
	@Autowired
	DeptService deptService; 
	// 근데 인터페이스 DeptService, 구현 BasicDeptService로 했는데 왜 basicDeptService로 받지 않음?
	// 그 이유는 자료형을 기반으로 자동을 찾아 주기 때문이다. 
	
	@GetMapping(value= {"", "/", "main"}) // 매핑 혹은 바인딩 , url과 메서드를 연결하기
	public String main(Model model) {
		
		model.addAttribute("serverTime", "서버시간");
		
		return "dept/main";
	}
	
	@GetMapping("count")
	public String getDeptCount(@RequestParam(value="deptno", required=false, defaultValue="0") int deptNo, Model model) {
		
		if (deptNo == 0) {
			model.addAttribute("count", deptService.getDeptCount());
		} else {
			model.addAttribute("count", deptService.getDeptCount(deptNo));
		}
		
		return "dept/main";
	}
	
	@GetMapping("list")
	public String getAllDepts(Model model) {
		
		List<Dept> deptList = deptService.getDeptList();
		
		model.addAttribute("deptList", deptList);
		model.addAttribute("dept", new Dept());
		
		return "dept/list";
	}
	
	@PostMapping("insert")
	public String insertDept(Dept dept, RedirectAttributes redirectAttributes) {
		log.debug(dept.toString());
		try {
			deptService.insertDept(dept);
			redirectAttributes.addFlashAttribute("message", dept.getDeptNo() + "번 부서가 등록되었습니다.");
		} catch(RuntimeException ex) {
			redirectAttributes.addFlashAttribute("message", ex.getMessage());
			
		}
		
		return "redirect:/dept/list";
	}
	
	
	@GetMapping("list/{deptno}")
	public String getDeptInfo(@PathVariable("deptno") int deptno, Model model) {
		
		List<Dept> deptList = deptService.getDeptList();
		Dept dept = deptService.getDeptInfo(deptno);
		
		model.addAttribute("deptList", deptList);
		model.addAttribute("dept", dept);
		
		return "dept/list";
	}
	
	@PostMapping("update")
	public String updateDept(Dept dept, RedirectAttributes redirectAttributes) {
		log.debug(dept.toString());
		try {
			deptService.updateDept(dept);
			redirectAttributes.addFlashAttribute("message", dept.getDeptNo() + "번 부서가 수정되었습니다.");
		} catch(RuntimeException ex) {
			redirectAttributes.addFlashAttribute("message", ex.getMessage());
			
		}
		
		return "redirect:/dept/list"; //처리된 내용을 바탕으로 DB를 다시 보고 그 결과로 HTML을 만들어야 하기에 리다이렉트
	}
	
	
	@PostMapping("delete")
	public String deleteDept(Dept dept, Model model, RedirectAttributes redirectAttributes) {
		log.debug(dept.toString());
		try {
			int cnt = deptService.deleteDept(dept.getDeptNo());
			
			if (cnt > 0) {
				redirectAttributes.addFlashAttribute("message", dept.getDeptNo() + "번 부서가 삭제되었습니다.");
				return "redirect:/dept/list";
			} else {
				redirectAttributes.addFlashAttribute("message", "부서 번호를 찾을 수 없습니다.");
				model.addAttribute("dept", deptService.getDeptInfo(dept.getDeptNo()));
				return "dept/list"; // 삭제한 후에 그 결과를 바탕으로 그대로 HTML로 보여주면 되기 때문에
				// return "redirect:/dept/list/" + dept.getDeptNo(); //위의 것과 이것은 같음
			}
		} catch(RuntimeException ex) {
			redirectAttributes.addFlashAttribute("message", ex.getMessage());
			return "redirect:/dept/list";
		}
	}
	
	
}

```

#### fragment 파일

아래와 같은 방식으로 **반복되는 부분인 header, footer 부분을 매 번 html에 구현할 필요 없이 fragment, 조각으로 나누어서 구현하고 조립할 수 있다.**

`default_layout.html` 파일 : 

```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org/"
	  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
<head>
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">
	<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.13.1/font/bootstrap-icons.min.css">
	<title>부서 관리</title>
	<script src="https://cdn.jsdelivr.net/npm/jquery@3.7.1/dist/jquery.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js"></script>
    <th:block layout:fragment="css"></th:block>
    
</head>
<body>
	<div class="container"> <!-- container 클래스는 bootstrap 것, 가운데 정렬 시켜줌 -->
		
		<div th:replace="/dept/include::header"></div>
		
		<th:block layout:fragment="content"></th:block>
		
		<div th:replace="/dept/include::footer"></div>
	</div>
	
	
	<th:block layout:fragment="script"></th:block>
</body>
</html>
```

`include.html` 파일: 

```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
    <div th:fragment="header">
    	<div style="margin-top:1em">
    		<i class="bi bi-bicycle" ></i>  header 영역입니다. 
	        <a href="./login" class="btn btn-outline-secondary"><i class="bi bi-airplane" style="color:skyblue;"></i>로그인</a>
	        <a href="./logout" class="btn btn-outline-secondary">로그아웃</a>
    	</div>
        
        <hr>
    </div>

    <div th:fragment="footer">
        <hr>
        footer 영역입니다.
    </div>
</html>
```

### list.html + javaScript(실제 화면 + 자바 스크립트)



```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org/"
	  xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
	  layout:decorate="~{dept/default_layout}"> <!-- defalut_layout에 있는 내용을 가져온다. 
      head, body 가 default_layout에 있기에 여기서는 HTML의 head, body 제거 -->

<!-- default_layout에서 content에 들어가 것 만 만들면 됨. -->
<div layout:fragment="content"> <!-- default_layout.html 에 있는 content 부분을 채우겠다는 의미 -->
	<h1>부서 목록</h1>
	<table border = "1" class="table table-hover"> 
		<thead>
			<tr>
				<th>부서 번호</th>
				<th>부서 명</th>
				<th>부서 위치</th>
			</tr>
		</thead>
		
		<tbody>
			<tr th:each="dept : ${deptList}" >
				<td>[[ ${dept.deptNo} ]]</td>
				<td><a th:href="@{/dept/list/{id}(id=${dept.deptNo},)}">[[ ${dept.dName} ]]</a></td>
				<td>[[ ${dept.loc} ]]</td>
			</tr>
		</tbody>
		
	</table>
	[[ ${message} ]]
	<div>
		<form action="./insert" method="post" id="frmForm">
			<ul th:object="${dept}">
				<li>
					<label>부서 번호 : </label>
					<input type="number" th:field="*{deptNo}" min="0" max="100" step="5" required>
				</li>
				<li>
					<label>부서 명 : </label>
					<input type="text" th:field="*{dName}" maxlength="14" required>
				</li>
				<li>
					<label>부서 위치 : </label>
					<input type="text" th:field="*{loc}" maxlength="13"  required>
				</li>
							
			</ul>
			
			<button type="submit" class="btn btn-primary" id="btnNew">신규등록</button>
			<button type="submit" class="btn btn-warning" id="btnUpdate">수정저장</button>
        	<button type="submit" class="btn btn-danger" id="btnDelete">삭제</button>
        	<button type="button" class="btn btn-secondary" id="btnReset">메인 화면</button>
			
		</form>
	
	</div>
	
</div>

<div layout:fragment="script">
	<script>
		// 현재 진행하는 방식은 jQuery를 이용하는 것인가? -> 맞음 

		// document.getElementById('frmForm') : element object 선택
		// jQuery에서는 $('#frmForm')처럼 CSS 선택자를 사용하여 요소를 선택하는 방식을 사용한다.

		// event : 지정된 함수가 실행될 시점.
		// callback, event handler : 이벤트에 의해서 실행되는 함수

		// frmForm id인 요소 객체(폼)에서 submit 이벤트가 발생하면, 뒤의 함수를 실행
		$('#frmForm').on('submit', function(e) { //여기서의 e는 'e' 또는 'event'는 이벤트를 나타내는 객체(Event Object)이다.
			// alert('sumbit 이벤트가 발생했습니다.')
			
            e.preventDefault(); // 이벤트 진행 중단 -> alert는 발생하지만, 서버로 데이터가 넘어가지 않음
            // 중단하지 않으면 바로 서버로 데이터가 가기에 action을 수정하기 위해서는 필수로 중지시켜야 한다.
			
			// 자바 스크립트 객체를 만듬 -> { }로 객체를 만듬. property(+get/set) : return value
			// 자바 스크립트 객체 표기법
			const url = { // 마치 자바에서 url 들을 담은 클래스를 만들고 get/set을 만든 것과 같다
				'btnNew' : '/dept/insert',	
				'btnUpdate' : '/dept/update',	
				'btnDelete' : '/dept/delete'
			};
            // JavaScript 객체 리터럴 문법을 사용하여 key-value 쌍의 객체를 생성
            // 각 버튼의 'id'를 키로, 해당 버튼 클릭 시 폼이 전송될 URL을 값으로 저장
            // JavaScript에서는 이런 간단한 데이터 묶음을 만들 때 클래스를 정의할 필요 없이 객체 리터럴을 주로 사용함.


			console.log(url['btnUpdate'], url.btnDelete); //url을 확인 가능
			
			// 자바에서는 어떤 객체가 이벤트를 발생시켰는지 알 수 있다. (이벤트 총괄 객체)
			const btn = event.submitter.id; // 어디서 sumbit이 되었는지 .id 반환
			console.log(btn, url[btn]); // id를 기반으로 url을 찾을 수 있다.
			
			$(this).attr({'action':url[btn]}).unbind('submit').submit();
			// this. -> form(frmForm) 의 속성 중 action을 업데이트, 현재의 submit은 중지 되었으니 취소하고, 새로 다시 submit 하겠다.
            // `$(this)`: 현재 이벤트가 발생한 요소, 즉 `#frmForm` (폼 요소)를 jQuery 객체로 감쌉니다.
            // `.attr({'action': url[btn]})`: 폼 요소의 `action` 속성 값을 동적으로 변경합니다. 예를 들어 '신규등록' 버튼을 눌렀다면 `action="/dept/insert"`로 변경됩니다.
            // `.unbind('submit')`: 현재 폼에 걸려있는 모든 'submit' 이벤트 핸들러(여기서는 현재 실행 중인 바로 이 함수)를 제거합니다. 이전에 `e.preventDefault()`로 폼 제출을 막았기 때문에, 새로운 제출을 시작하기 전에 이전에 등록된 이벤트가 또 발동하지 않도록 방지하는 중요한 단계입니다.
            // `.submit()`: 변경된 `action` URL로 폼을 수동으로 다시 제출합니다.
            // 추가로 이 과정에서는 다시 이벤트를 중지시키고 객체를 확인하여 url 매핑하는 과정을 하지 않는다. 즉 위의 코드는 1번만 실행된다.
		}); 
		
	
		$('#btnReset').on('click', function() {
			//console.log('초기화 버튼이 클릭되었습니다.')
			location.href="/dept/list";
		}).on('mouseover', function() {
			console.log('포인터가 들어왔습니다.')

		}).on('mouseout', function() {
			console.log('포인터가 나갔습니다.')

		});
		
	</script>

</div>
	
</html>
```

1. jQuery 활용: $ 기호로 시작하는 코드는 jQuery 라이브러리를 사용하며, DOM 요소 선택($('#frmForm'))과 이벤트 처리(.on('submit'))를 간결하게 수행합니다.

2. 이벤트 객체와 기본 동작 중단: 폼 제출(submit) 이벤트 발생 시, 콜백 함수의 e (이벤트 객체)를 통해 e.preventDefault()를 호출하여 폼의 기본 제출 동작을 중단시킵니다. 이는 다음 단계에서 폼의 action URL을 동적으로 변경하기 위해 필수적입니다.

3. 동적 URL 할당: JavaScript 객체 리터럴(const url = { ... })을 사용하여 각 버튼 ID에 해당하는 서버 URL을 미리 정의합니다.

4. event.submitter를 이용한 버튼 식별: 폼을 제출한 버튼이 무엇인지(event.submitter.id)를 파악하여, 해당 버튼의 ID에 맞는 URL을 url 객체에서 찾아냅니다.

5. 폼 action 변경 및 재제출: 찾은 URL로 폼의 action 속성을 변경($(this).attr({'action': url[btn]}))한 후, 기존 submit 이벤트 핸들러를 제거(unbind('submit'))하고 **폼을 수동으로 다시 제출(.submit())**합니다. 이 과정을 통해 하나의 HTML 폼으로 '신규등록', '수정저장', '삭제'와 같은 여러 기능을 유연하게 처리할 수 있습니다.

6. 페이지 이동: '메인 화면' 버튼 클릭 시 location.href="/dept/list"를 통해 특정 URL로 페이지를 이동시킵니다.


**이벤트 객체 e**
'e' 또는 'event'는 이벤트를 나타내는 객체(Event Object). 이 객체는 발생한 이벤트에 대한 다양한 정보(예: 어떤 요소에서 발생했는지, 마우스 좌표 등)를 담고 있다. 여기서는 폼 제출(submit) 이벤트에 대한 정보를 가지고 있다.


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### url을 입력할 때 어떤 것은 메서드로 가고, 어떤 것은 페이지로 가는 이유는?

리다이렉트는 메서드를 호출하는 거고, 그냥 쓰면 html을 부르는 것이다. 

우리가 url을 부르면 html을 호출하는 것이 아니라 컨트롤러 메서드를 호출하는 것이다. 

하지만 둘을 같게 해서 관리하기 편하게 해서, 혼동이 온 듯

결국 우리가 url을 부를 때는  컨트롤러의 메서드를 부르는 것이다.

## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
