---
layout: post
title: "8월 4일 월요일 TIL(RESTful API)"
date: 2025-08-04 18:00:00 +0900
categories: July_week6
---

# 8월 4일 월요일 TIL 작성

## 1. 학습 주제

- RESTful API

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### 이전 내용 정리, 생각해 볼 것(Spring, Spring boot)

스프링 프로젝트의 장점과 단점? 스프링 부트는? → 내가 사용하는 기술의 장단점과 언제 사용하는지, 왜 사용하는지 등을 정리하기

스프링 부트는 애플리케이션 단위이고, 각각의 서버를 가지고 있기에 MSA 에 유리할 수 있다.

컨트롤러는 어느 뷰에 보낼지와 뷰에 보낼 데이터를 담은 모델을 알고 있다. 웹 리퀘스트는 컨트롤러로 전달된다.

VO는 불변형, 생성자로 초기화 하고, get만 가지고 있음. 도메인도 비슷함. 왜나하면 DB의 내용이 임의로 바뀌면 안되니까

서버 사이드 렌더링 / 클라이언트 사이드 렌더링 → 어떤 차이점이 있고 어떻게 작동하는가

기본 HTML을 주고, 개인마다 서로 다른 이미지 CSS 등을 적용하는 것? 

점점 개인화 된 서비스를 원하고 서비스가 증가하고 있다. → 인공지능을 통해서 내가 모르는 나에 대해서 , 금융의 개인화(관심있는 도메인)


### RESTful  (Representational State Transfer)

HTTP 의 메서드를 이용해서 url에서 /insert , /update 이렇게 여러 url을 만드는 것을 피하자.

url → uri 로 확장?

URL에서 URI로 바꾼 이유? → 큰 용어의 변경은 아니고, 예전에는 HTML의 링크를 중요히 여겼지만, 요즘에는 HTML이 아닌 CSS나 이미지 등의 리소스를 중요히 여기기에 link를 신경쓰는 것이 아니라 (resource)의 identifier를 말하는 URI 라는 용어를 쓰기 시작했다.

그럼 HTML은 그대로 두고 , 그 안의 이미지나 개인화된 정보만 새로 갱신(update), 리스소만 업데이트 하고자 할 때 어떻게 해야할까? 이를 XMLHTTPRequest라고 하고 XHR 라고 하며, **ajax**이라고 한다. ajax은 HTML을 요청하는 것이 아니라 리소스를 요청한다.

#### ajax?

서버에서 HTML을 보내지 말고, 데이터를 보내자. 리소스의 개념이 단순 HTML에서 확장됨.

#### HTTP 메서드 사용

- get: 자원을 조회하는데 사용(읽기)
- post: 새로운 자원을 생성하기 위해 사용한다.(생성)
- put: 기존 자원을 업데이트 하기 위해서 사용한다.(업데이트)
- delete: 자원을 삭제하기 위해서 사용한다. (삭제)

#### RESTful 스타일
REST라는 아키텍쳐 **스타일**을 따르는 웹 서비스, HTTP 프로토콜을 사용하여 클라이언트와 서버 간에 데이터를 교환

REST는 웹 서비스의 모든 것을 자원(resource)로 간주하며, 자원은 URI(Uniform Resource Identifier)를 통해 식별됨 , URI는 HTML 의 주소만을 나타내는 것이 아니라, 여러 변수, 리소스를 지칭할 수 있다. 리소스로 접근할 수 있는 변수명, get과 같은 URI를 통한 접근 path 라고 할 수 있다.

json 은 자바스크립트 객체를 표기하는 방법이다. 단 파일로 만듬

자바스크립트는 get/set으로 접근하지 않고, 그대로, 이름으로 사용 가능하다. get/set을 동시에 이름으로 사용할 수 있는 것을 **‘property’ 라고 한다.**
**따로 get이나 set 같은 메서드를 호출하지 않고도 바로 점(.) 표기법이나 대괄호([]) 표기법으로 접근할 수 있는데, 이렇게 접근하는 대상**을 바로 **프로퍼티(property)**라고 합니다.

### DeptAPIController.java 파일 내용

- `@RestController` : 이 클래스가 Spring MVC의 RESTful 웹 서비스 컨트롤러임을 나타낸다. `@Controller`와 `@ResponseBody`의 조합과 같다. 즉, 이 클래스 안의 모든 메서드는 반환 값을 View 이름으로 해석하지 않고, HTTP 응답 본문(Body)으로 직접 직렬화(JSON 또는 XML 등)하여 전송한다.

- RESTful URI 규칙: 주석에 언급된 것처럼 URI를 소문자로, 계층형으로, 리소스를 복수형 명사(depts)로 지정하는 것은 RESTful API 가 주로 사용하는 방식이다. 이는 직관적이고 예측 가능한 API를 만들어야 한다.

- Map<String, Object> 반환: 대부분의 API 메서드가 Map<String, Object>를 반환합니다. `@RestController`에 의해 이 Map은 자동으로 JSON 형식으로 변환되어 클라이언트에 응답됩니다. 이를 통해 상태 메시지나 데이터 목록 등을 유연하게 포함할 수 있습니다.

- 예외 처리: try-catch 블록을 사용하여 서비스 계층에서 발생할 수 있는 RuntimeException을 처리하고, 결과를 message 키로 Map에 담아 클라이언트에 전달합니다. 이는 API 호출 결과에 대한 피드백을 제공하는 좋은 방법입니다. (더 발전된 형태로는 @ControllerAdvice를 이용한 전역 예외 처리가 있습니다.)

```java
//import 부분 생략

@Slf4j 
@RestController // URI 규칙, Resource 위주로 -> json 파일을 리턴
@RequestMapping("api/depts") // URI는 소문자로 계층형, 리소스를 복수형 명사로 지정
public class DeptAPIController {
	
	@Autowired
	DeptService deptService; 
	
	//기본 HTML(json을 보여줄?)
	@GetMapping(value= {"index", "/index"}) // 매핑 혹은 바인딩 , url과 메서드를 연결하기
	public ModelAndView main(Model model) {
		
		ModelAndView mv = new ModelAndView("api/list");
		
		return mv;
	}
	
	
	@GetMapping("")
	public Map<String, Object> getAllDepts(Model model) {
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		//RESTful을 더 제대로? 쓰려면 -> ResponseEntity : status code, header, body 를 설정 가능
		
		List<Dept> deptList = deptService.getDeptList();
		
		resultMap.put("deptList", deptList);
		
		return resultMap; //맵을 통해서 데이터에 대해서 접근할 key와 데이터인 value 형태로 반환
	}
	
	
	@PostMapping("/")
	public Map<String, Object> insertDept(Dept dept) {
		log.debug(dept.toString());
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		try {
			deptService.insertDept(dept);
			resultMap.put("message", dept.getDeptNo() + "번 부서가 등록되었습니다.");
		} catch(RuntimeException ex) {
			resultMap.put("message", ex.getMessage());
			
		}
		
		return resultMap;
	}
	
	
	@GetMapping("/{deptno}")
	public Dept getDeptInfo(@PathVariable("deptno") int deptno, Model model) {
			
		return deptService.getDeptInfo(deptno);
	}
	
	@PutMapping("/")
	public Map<String, Object> updateDept(Dept dept) { 
		log.debug(dept.toString());
		
		Map<String, Object> resultMap = new HashMap<String, Object>();

		try {
			deptService.updateDept(dept);
			resultMap.put("message", dept.getDeptNo() + "번 부서가 수정되었습니다.");
		} catch(RuntimeException ex) {
			resultMap.put("message", ex.getMessage());
			
		}
		
		return resultMap;
	}
	
	
	@DeleteMapping("/{deptno}") //delete 는 payload 가 불가능, form 을 통해서 정보를 보낼 수 없다. url부분에 들어가야함.
	public Map<String, Object> deleteDept(@PathVariable("deptno") int deptno) { // 삭제는 PathVariable 이 필요하다.
		log.debug(Integer.toString(deptno));
		
		Map<String, Object> resultMap = new HashMap<String, Object>();
		
		try {
			int cnt = deptService.deleteDept(deptno);
			
			if (cnt > 0) {
				resultMap.put("message", deptno + "번 부서가 삭제되었습니다.");

			} else {
				resultMap.put("message", "부서 번호를 찾을 수 없습니다.");
			}
		} catch(RuntimeException ex) {
			resultMap.put("message", ex.getMessage());
		}
		
		return resultMap;
	}
	
}

```

#### 각 API 메서드 분석
**`@GetMapping(value= {"index", "/index"}) public ModelAndView main(Model model)`**

용도: "기본 HTML(json을 보여줄?)" 이라는 주석처럼, 이 메서드는 JSON 데이터를 직접 반환하는 API가 아니라, 부서 정보를 보여줄 웹 페이지(HTML 뷰)를 반환한다. 즉, API 클라이언트가 아니라 일반 웹 브라우저 사용자를 위한 엔드포인트

ModelAndView: View 이름(여기서는 api/list)과 Model 데이터를 함께 반환할 때 사용. api/list는 /WEB-INF/views/api/list.jsp나 api/list.html 같은 템플릿 파일을 가리킨다.

@RestController가 붙어있지만, 이 메서드만 ModelAndView를 반환하도록 설계되어 HTML 뷰를 제공합니다. 이는 @RestController의 일반적인 사용법은 아니지만, 특정 엔드포인트에서 뷰를 반환할 수 있도록 Spring이 허용하는 방식입니다. (더 명확하게는 DeptAPIController를 @Controller로 두고 각 API 메서드에 @ResponseBody를 붙이거나, API용 컨트롤러를 따로 분리하는 것이 일반적입니다.)

**`@GetMapping("") public Map<String, Object> getAllDepts(Model model)`**

용도: 모든 부서 목록을 조회하는 API, GET /api/depts 요청에 응답한다.

`List<Dept> deptList = deptService.getDeptList();` : 서비스 계층을 통해 모든 부서 데이터를 가져옵니다.

resultMap.put("deptList", deptList);: 가져온 부서 목록을 deptList라는 키로 Map에 담아 반환하고, @RestController에 의해 이 Map이 JSON으로 변환됩니다.

**`@PostMapping("/") public Map<String, Object> insertDept(Dept dept)`**

용도: 새로운 부서 정보를 **생성(등록)**하는 API, POST /api/depts 요청에 응답한다.

Dept dept: 클라이언트로부터 전송된 폼 데이터 또는 JSON 데이터를 Dept 객체에 자동으로 바인딩합니다. 

**`@GetMapping("/{deptno}") public Dept getDeptInfo(@PathVariable("deptno") int deptno, Model model)`**

용도: 특정 부서의 상세 정보를 조회하는 API, GET /api/depts/{deptno} (예: /api/depts/10) 요청에 응답한다.

@PathVariable("deptno") int deptno: URI 경로의 {deptno} 부분에 있는 값을 추출하여 deptno 변수에 바인딩합니다. 예를 들어 /api/depts/10 요청 시 deptno는 10이 됩니다. RESTful API에서 특정 리소스(여기서는 특정 부서)를 식별할 때 이 방식을 사용합니다.

return deptService.getDeptInfo(deptno);: 조회된 Dept 객체가 JSON으로 변환되어 반환됩니다.

**`@PutMapping("/") public Map<String, Object> updateDept(Dept dept)`**

용도: 기존 부서 정보를 **수정(업데이트)**하는 API, PUT /api/depts 요청에 응답한다.

Dept dept: POST와 유사하게 클라이언트가 보낸 수정할 데이터를 Dept 객체에 바인딩합니다.

**`@DeleteMapping("/{deptno}") public Map<String, Object> deleteDept(@PathVariable("deptno") int deptno)`**

용도: 특정 부서 정보를 삭제하는 API입니다. DELETE /api/depts/{deptno} 요청에 응답합니다.

delete 는 payload 가 불가능, form 을 통해서 정보를 보낼 수 없다. url부분에 들어가야함.: 주석의 내용처럼, DELETE 메서드는 HTTP 스펙상 요청 바디(payload)를 가질 수 있지만, 웹 브라우저의 <form> 태그는 GET과 POST만 지원합니다. 따라서 웹 페이지에서 DELETE 요청을 보내려면 JavaScript(AJAX)를 사용해야 하며, 삭제할 리소스의 식별자(여기서는 deptno)를 URL 경로에 포함하는 @PathVariable 방식이 일반적이고 RESTful 원칙에 부합합니다.

삭제는 PathVariable 이 필요하다.: 정확합니다. 삭제 대상을 명확히 지정하기 위해 URI 경로 변수로 deptno를 받는 것이 가장 RESTful한 방식입니다.

### list.html (RESTful 방식)

```HTML
	<script>
		
		// ajax(비동기 요청 처리)
		function getDetail(deptno) {
			//가운데 {} 는 파라미터, 현재는 추가 매개변수 없음
			// 이런 방식으로 요소를 선택하고 DOM을 변경하는 것으로 화면을 다시 렌더링한다.
			$.get('/api/depts/' + deptno,{},function (dept) {
				$("#deptNo").val(dept.deptNo);
				$("#dName").val(dept.dName);
				$("#loc").val(dept.loc);
			}); 
		}
		
		//getDetail(10);
		
		
		
		function getList() {
			
			let result = '';
			
			$.get('/api/depts',{},function(list) {
				console.log(list, list.deptList[0]);
				
				for(let dept of list.deptList) {
					console.log(dept);
					
					//` `를 통해서 변수를 그대로 사용 가능, 문자열에서?
					// 화면을 만들기 위해서 요소 객체를 생성
					result += `
					<tr>
						<td >${dept.deptNo}</td>
						// 이 부분 잘 모르겠음
						<td><a href="#" class="details" data-deptNo="${dept.deptNo}">${dept.dName}</a></td>
						<td>${dept.loc}</td>
					</tr>
					
					`;
				} 
				
				//$('#dept_table > tbody').append(result); //현재 메모리에 계속해서 추가함. 페이징 할 때 좋음. 그러나 현재 우리가 원하는 기능은 아님
				$('#dept_table > tbody').empty().append(result);
			});
		
		}
		
		// DOM 생성 이전에도 실행 가능
		//getList();
		
		// DOM 생성 이후에 사용
		$(document).ready(function() {
			getList();
		})
		.on('click','.details',function() {
			const deptno = $(this).attr('data-deptNo');
			console.log(deptno);
			getDetail(deptno);
		});
		
		
		// 버튼별로 다르게 작동하도록, 어떻게 작동하는 지 공부하기!!
		$('#frmForm').on('submit', function(e) { 
			e.preventDefault(); 
			

			const method = {
				'btnNew' : 'POST',	
				'btnUpdate' : 'PUT',	
				'btnDelete' : 'DELETE'
			};
			

			const btn = event.submitter.id; 
			console.log(btn, method[btn]); 
			
			let deptno = '';
			
			if (method[btn] == 'DELETE') {
				deptno = $('#deptNo').val();
			}
			
			$.ajax({
				type: method[btn],
				url: '/api/depts/'+deptno,
				data: $('#frmForm').serialize(),
				success: function(result) {
					alert(result.message);
					getList();
					$("#deptNo").val('');
					$("#dName").val('');
					$("#loc").val('');
				},
				error : function(request,status, error) {
					console.log(error);
				}
			}	
			);
			
		}); 

	</script>
```



## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
