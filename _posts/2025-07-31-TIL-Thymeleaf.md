---
layout: post
title: "7월 31일 목요일 TIL(Thymeleaf)"
date: 2025-07-31 18:00:00 +0900
categories: July_week5
---

# 8월 일 요일 TIL 작성

## 1. 학습 주제

- Thymeleaf

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### 스프링 총정리

스프링으로 프로젝트를 할 때, 프로그램 코드 부분은 컴포넌트 스캔으로 처리가 가능하다.

실력을 더 향상 시키고 싶다면, JEE 부분, 설정파일을 만드는 부분, 설계 부분을 공부하는 것이 더 높은 쪽으로 갈 수 있다.

스프링은 중요한 것 중 하나는 **의존성 주입**이다. 이 부분을 말할 수 있어야 한다. 6개의 어노테이션을 알고 있어야 한다. 의존성은 클래스 간의 의존성, 스프링이 만든 빈을 통해서 의존성을 주입하고 클래스간 의존성을 해결한다.

라이브러리 의존성은 maven이나 gradle을 통해서 해결한다. 필요한 라이브러리를 설정하면, 그것에 필요한 하위 라이브러리 등을 같이 관리해 준다. 이것은 building 할 때 필요하다.

스프링은 하나의 큰 프로젝트 안에 모든 것이 들어있다.

### 스프링 부트

스프링 부트는 각각의 기능, 도메인 별로 스프링 부트 어플리케이션(프로젝트)를 각각 개발한다. 그리고 view를 만들지 않고 API를 통해서 데이터만 넘겨준다.(MSA) 

스프링 부트 프로젝트 생성할 때 작성하는 패키지를 기준으로 컴포넌트 스캔을 진행한다. 되도록 상위 패키지로 설정하면 여러 패키지를 일일히 컴포넌트 스캔 설정을 해줄 필요가 없어진다.

템플릿 엔진 : Thymeleaf, jsp 등 HTML, XML, JavsScript,CSS 및 일반 텍스르를 처리할 수 있는 엔진.

### 일반적인 웹 프로젝트 구현 순서

**순서**

 1. 패키지 →  2. 모델 → 3. DAO(Repository) → 4. SQL Mapper → 5. Service → 6. Controller →  7. view

- 모델 : DB에 있는 컬럼 명을 담을 private 멤버 변수를 선언한다. 모델은 관심사가 DB이다.
- dao(repository) : db와 연관된 메서드만 포함한다. 인터페이스만 만들고 Mybatis에서 mapper를 만드는 식으로 구현, DAO는 DB만 신경쓴다.
- SQL mapper : 위에서 만든 인터페이스를 구현한 실제 SQL mapper를 생성한다.
- dto : DB에 집중된 모델과, DAO를 서비스와 뷰에서 사용할 수 있도록 변환? 전달하는 것, 서비스는 view에 관심이 있기에 dto로 둘 사이를 이어주는 것이 필요하다. **따라서 모델과 dto 사이의 서로 변환 과정이 필요한데,** 이를 할 때 builder 패턴을 이용할 수 있다.
- service : 비지니스 로직과 관련되어 있고, view에서 보여줄 데이터를 만든다.(단 출력과 관계된 것은 하지 않음)
- controller : service를 사용하여 view에게 어떤 것을 보여줄지를 컨트롤한다.


### Thymeleaf 문법

`basic.html` 파일 : 기본적인 Thymeleaf 문법 내용 담고 있음

basic.html은 Thymeleaf의 핵심 문법과 기본 표현식들을 망라하여 보여주는 예제 파일입니다.

주요 내용:

- EL (표현 언어): $, #, *, @, ~ 등 다양한 Thymeleaf 표현식의 사용법을 보여줍니다. 특히, session, param, request 스코프의 데이터 접근 방법과 삼항 연산자 등을 예시합니다.

- 주석 처리: HTML 기본 주석과 Thymeleaf 전용 주석(, )의 차이를 설명합니다.

- 변수 및 연산자: th:with를 이용한 변수 선언, 숫자/문자열/boolean 값 다루기, 비교/산술 연산자, 그리고 **Elvis 연산자(?:)**와 **No-Operation 연산자(?:_)**의 활용을 보여줍니다.

- 조건문 (th:if, th:unless, th:switch): 특정 조건에 따라 HTML 요소를 렌더링하거나 다른 내용을 표시하는 방법을 제시합니다. th:block 태그로 불필요한 HTML 태그 없이 Thymeleaf 로직을 적용하는 예시도 포함합니다.

- 반복문 (th:each): 리스트 컬렉션(list)을 순회하며 요소를 출력하는 방법과, stat 객체를 활용하여 반복문의 상태(인덱스, 카운트, 첫/마지막 요소 여부 등)를 확인하는 방법을 보여줍니다. th:each를 이용한 숫자 시퀀스 생성(#numbers.sequence) 예시도 있습니다.

- 폼 처리 및 속성 조작 (th:object, th:field, th:attr, th:classappend, th:data-*): HTML 폼 요소에 서버에서 넘어온 데이터를 바인딩(th:field)하거나, 동적으로 속성(class, name, value, readonly, required, placeholder)을 추가/변경하는 방법을 설명합니다. 체크박스(th:checked), 라디오 버튼, 드롭다운(select, option)과 같은 폼 입력 요소의 처리도 다룹니다.

- 조각(Fragment) 삽입 (th:insert, th:replace): 다른 HTML 파일의 특정 부분을 가져와 현재 문서에 삽입하는 th:insert와 th:replace의 차이점을 설명합니다. 조각에 파라미터를 전달하거나 조건부로 삽입하는 예시도 포함합니다.

- JavaScript 연동: Thymeleaf 변수를 JavaScript 코드 내에서 사용하는 방법과, th:inline="javascript"를 사용하여 더 안전하고 편리하게 자바 객체를 JavaScript 변수로 변환하는 방법을 보여줍니다.


```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org/">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	.text {background-color:Skyblue;}
	.large { height:35px; }
	.bold {font-weight:bold;}
	.active {background-color:Skyblue;}
	.deactive {background-color:gray;}
</style>
</head>
<body>
	<h1>Thymeleaf</h1>
	
	<h2>1. EL</h2>
	<h3>EL 문법</h3>
	<ul>
		<li th:text="${session.msg }"></li> <!-- session -->
		<li th:text="${param.name }"></li>	<!-- Query Parameter : url 뒤에 ?name=입력값 으로 사용 가능(get 방식) -->
		<li th:text="${a }"></li>			<!-- request : key 값만 사용하면 됨 -->
		
		<li th:text="${a+10 }"></li>
		<li th:text="'${1+2 } =' + ${1+2 }"></li>
		<li th:text="${a > 0 ? 'big' : 'small	'}"></li>
	</ul>
	
	<h3>1.2 주석</h3>
	<ul>
        <li>HTML: <!-- 기본 주석 --> &lt;!-- 기본 주석 --&gt;</li>
        <li>Thymeleaf: <!--/* 타임리프 주석 - 출력안됨 */-->  &lt;!--/* 타임리프 주석 - 출력안됨 */--&gt; </li>
        <li>Thymeleaf: <!--/*/ 타임리프 주석 - 실행되면 출력, 실행안되면 주석처리 /*/-->  &lt;!--/*/ 타임리프 주석 - 실행되면 출력, 실행 안되면 주석처리 /*/--&gt;</li>
    </ul>
	
	<h2>2. 표현식</h2>
	<div>
        ${...}:    변수의 값,  *{...}: 선택 변수 표현식, #{...}: 메시지(message.properties, 국제화) 표현식, @{...}: URL 표현식, ~{...}: 조각 표현식
    </div>
    <h3>${...} @{...}</h3>
    <ul>
        <li th:text="'메시지는 &quot;<b>' + ${session.msg } + '</b>&quot; 입니다.'"></li>
        <li th:utext="'메시지는 &quot;<b>' + ${session.msg } + '</b>&quot; 입니다.'"></li>
        <li>메시지는 &quot;[[ ${msg } ]] &quot; 입니다.</li>
        <li>메시지는 &quot;[( ${msg } )] &quot; 입니다.</li>
        <li th:utext="|메시지는 &quot;<b>${session.msg }</b>&quot;입니다.|"></li>
        <li>스프링 빈: [[ ${@defaultTemplateBasicService.getMap()['A']} ]]</li>
    </ul>
    <ul>
        <li><a th:href="@{/home/basic}">단순url</a></li>
        <li><a th:href="@{/home/basic(name=${user.userName}, id=${user.userId},)}">쿼리파라메터</a></li>
        <li><a th:href="@{/home/basic/{name}/{id}(name=${user.userName}, id=${user.userId},)}">경로파라메터</a></li>
        <li><a th:href="@{/home/basic{id}(name=${user.userName}, id=${user.userId},)}">쿼리파라메터</a></li>
        
    </ul>
	
	<h3>*{...}</h3>
	<ul th:object="${user}">
		<li th:text="${user.userId}"></li>
		<li th:text="*{userName}"></li> <!-- 상위에 객체를 미리 정의하고 *를 통해서 사용 -->
	</ul>
	
	<h2>3. Data, 연산자</h2>
	<ul th:with="val=${str }, val1='Hello', val2=null, val3=''"> <!-- ul 안에서 사용할 변수를 선언 -->
		<!-- 정의되지 않은 변수는 null로 처리됨 -->
		<!-- null 값이 'null'로 그대로 출력함 ->  -->
		<li th:text="|str: ${str}, val: ${val}, val1 = ${val1}, val2 : ${val2}, val3: ${val3}, val4: ${val4}|"></li>
		<li th:text="|num: ${num} x 2 = ${num*2}|"></li>
		
		<li>
			<span th:text="1 > 2"></span>,
		 	<span th:text="1 == 2"></span>,
		  	<span th:text="1 != 2"></span>
		</li>
		
		<li>
			<div th:text="(3 % 2 == 0)?'짝수':'홀수'"></div>
		</li>
		
		<li>Elvis 연산자:<span th:text="${val1}?:'값 없음'"></span>,<!-- null 이 아니면 val1, null 이면 값 없음 -->
		 No-Operation:<span th:text="${val1}?:_"></span></li>
		 
		 <li>Elvis 연산자:<span th:text="${val2}?:'값 없음'"></span>,
		 No-Operation:<span th:text="${val2}?:_"></span></li> <!-- _ 는 공백으로 출력된다. -->
		 
		 <li>Elvis 연산자:<span th:text="${val3}?:'값 없음'"></span>,
		 No-Operation:<span th:text="${val3	}?:_"></span></li> <!-- val3 = ''라서 아무것도 출력되지 않음 -->
		
	</ul>
	
	<h2>4. if, for</h2>
	<h3>4.1 if</h3>
	<ol>
		<li><span th:if="${a > 5}">크다</span>, <span th:if="${a <= 5}">작다</span></li>
		<li><span th:if="${a > 5}">크다</span>, <span th:unless="${a > 5}">작다</span></li> <!-- unless : 조건이 아니라면 -->
		<li>
			<th:block>	<!-- 태그 없이 thymeleaf를 사용할 때 -->
				<th:block th:if="${a>5 and a<15}">조건 만족</th:block>
				<th:block th:if="${a<=5 or a>=15}">조건 불만족</th:block>
			</th:block> 
		</li>
		<li th:with="str='E'" th:switch="${str}"> 
			<span th:case="A">90~</span>
            <span th:case="B">80~89</span>
            <span th:case="C">70~79</span>
            <span th:case="*">~69</span>
		</li>
		<li th:with="even=true, status='alert'">
			<div th:text="${even} ? '짝수' : '홀수'"></div>
			<div th:text="${even}?(${status=='alert'?'경고':'정상'}):'홀수'"></div> <!-- 3항 연산자 중첩 -->
			<div th:text="${even}?(${status=='alert'?:'정상'}):'홀수'"></div> <!-- true 쪽을 입력하지 않으면 논리값 true 가 출력 -->
			<div th:text="${even}?(${status=='alert'?_:'정상'}):'홀수'"></div> <!-- 아무것도 출력하고 싶지 않다면 _ 사용하기 -->
		</li>
	</ol>	
	
	<h3>4.1 loop(for 문)</h3>
	<ul>
		<li>
			<span th:each="item : ${list}">[[ ${item } ]], </span> <!-- 리스트 안의 요소만 가져오기 -->
		</li>
		<li>
			<th:block th:each="item, stat : ${list}"> <!-- 리스트 안의 요소를 구체적으로 활용, stat는 현재 행의 정보를 확인할 때 보통 사용한다.-->
				<div th:text="| ${stat.index}, ${stat.count}, ${stat.size}, ${stat.even}, ${stat.odd},
                ${stat.first}, ${stat.last}, ${stat.current} : ${item} |"></div>
			</th:block>
		</li>
		<li>
			<div th:with="nums=${#numbers.sequence(1,10)}"> <!-- range 같은 함수 -->
                <span th:each="i: ${nums}">[[${i }]],</span>
            </div>
		</li>
	</ul>
	
	
	<h2>5. Form, Attribute</h2>
	<form action="./basic" method="post" th:object="${user}"> <!-- 아래서 get/set 이름 사용 위해 -->
		<!-- attappend 에서 왜 붙이려면 한 칸 띄워야 해? -> 클래스 간의 구분 위해 --> <!-- text large 이렇게 하기 위해 -->
		<!-- classappend 를 하면 자동으로 띄어서 붙여짐. -->
		<input type="text" id="idx" name="기본값" class="text" 
				th:attrappend="class=' large'" th:classappend="bold" 
				th:name="*{idx}" th:value="*{idx}"
				/>  <!-- name:부분이 get/set 이름과 같게, value 부분은 현재 들어있는 값이 들어가도록 -->
		<input type="text" th:field="*{userId}" required 
				th:attr="readonly=true, required=true, placeholder=${msg}"/>
		<input type="text" th:field="*{userName}"
				th:data-el="*{userName}"/>
		<!-- th:data 사용자 정의 속성, 근데 이게 뭐하는건데? -->
		<th:block th:if="true">
			<input type="text" th:class="${false?'active':'deactive'}" th:required="${true}" />
			
			<!-- 항목이 많고, 1개만 선택할 때 select 사용 -->
			<select id="userRole" name="userRole" 
                	th:with="nums=${#numbers.sequence(1,10)}">
            	<option value="">선택해 주세요.</option>
            	<option th:each="num : ${nums}" th:value="${num}" th:text="${num}" th:selected="${num==5}"></option>
            </select>	
		</th:block>
		
		<div>
			<!-- 여러 항목이고 중복 선택일 때 checkbox / checkbox 는 label 로 감싸 주어야 한다. -->
			<label> 
				<input type="checkbox" th:field="*{userStatus}" th:text="1" th:value="1"/>
			</label>
			<label> 
				<input type="checkbox" th:field="*{userStatus}" th:text="2" th:value="2"
				/>
			</label>
		</div>
		<!-- checkbox는 여러개 를 배열로 받음 -->
		<div>
			<label> 
				<input type="checkbox" name="agree"  th:text="동의함" th:value="Y" th:checked="true"/>
			</label>
		</div>
		<!-- radio는 여러 개중 1가만을 골라서 반환함. -->
		<div>
            <label><input type="radio" name="userCheck" id="userCheck1" th:value="1" th:text="yes" th:checked="${a==10}"/></label>
            <label><input type="radio" name="userCheck" id="userCheck2" th:value="2" th:text="no" th:checked="${a!=10}"/></label>
        </div>
        
        <div>
        	<button type="submit">제출</button>
        </div>
	
	</form>
	
	
	<h2>6. Fragment ~{}</h2>
	<ul>
		<li>
			<!-- replace는 th: 가 선언된 태그를 그대로 두면서 부품으로 교체 -> 여기서는 div 가 2번 겹침 -->
			<div th:insert="~{/home/fragment :: frag01}" style="background-color : olive"></div>
		</li>
		<li>
			<!-- replace는 th: 가 선언된 태그를 없애고 부품으로 교체, 따라서 그곳에서 선언한 다른 것은 다 사라짐, color 적용 X -->
			<div th:replace="~{/home/fragment :: frag01}" style="background-color : olive"></div>
		</li>
		<li>
			<!-- ~{}를 생략 할 수 있지만, 보통 넣는걸로 작성한다. -->
			<div th:insert="/home/fragment :: frag01" style="background-color : olive"></div>
		</li>
		<li>
			<!-- 삼항 연산자와 조각을 통해서 조건에 따라서(로그인 여부 등) 보이는 것을 다르게 할 수 있다. -->
			<div th:replace="${true}?~{/home/fragment :: frag01}:_" style="background-color : olive"></div>
		</li>
		<li>
			<!-- 조각에 파라미터를 넣을 수 있다. -->
			<div th:replace="~{/home/fragment :: frag02('가','나')}" style="background-color : olive"></div>
		</li>
		<li>
			<div th:replace="~{/home/fragment :: frag03(~{:: .msg})}"> <!--/* css selector */-->
				<!-- 이 중에 뭐를 넘길지 위에서 고르고 넘김 -->
	            <i class="title">Title</i>
	            <i class="msg">Message</i>
	        </div>
		</li>
	</ul>
	
	<h2>7. Utility Object</h2>
	
	<div th:replace="~{/home/util-object :: utilFragment}"></div>
	
	<h2>8. JavaScript</h2>
	<!-- 모델이나, 세션등으로 넘어온 자바 데이터를 어떻게 다시 자바스크립트로 줄 것인가 -->
	<!-- 브라우저가 JS를 실행할 수 있게 소스 코드를 문자열로 전달해줌. -->
	<script>
		let msg = "[[${session.msg }]]";
	    let msg2 = "[(${msg })]";
	    let msg3 = "/[[${session.msg}]]/";
	    let name = "[[${param.name }]]";
	    let num = [[${a }]];
	    
	    /* let user = [[${user }]]; */
	</script>
	
	<script th:inline="javascript">
    /* <![CDATA[ */
        msg = [[${session.msg }]];
        msg2 = "[(${msg })]";
        msg3 = /[[${session.msg}]]/;
        name = [[${param.name }]];
        num = [[${a }]];

        let user = [[${user }]];

        console.log(user.userName);

        [# th:each="item, stat: ${list}" #]
        var num[[${stat.index}]] = [[${item}]];
        [/]
    /* ]]> */
    </script>
	

</body>
</html>

```

`fragment.html` : thymeleaf의 fragment 기능 확인을 위한 코드


fragment.html은 basic.html에서 호출하여 사용되는 Thymeleaf 조각(Fragment)들을 정의하는 파일입니다.

주요 내용:

- th:fragment: 이 어노테이션을 사용하여 재사용 가능한 HTML 코드 블록, 즉 "조각"을 정의합니다.

- frag01: 가장 기본적인 형태의 조각으로, 단일 HTML 요소를 포함합니다.

- frag02(param01, param02): 조각에 파라미터를 전달하여 동적으로 내용을 채울 수 있는 예시를 보여줍니다.

- frag03(title): 조각 내에서 th:replace를 사용하여 전달받은 파라미터(여기서는 HTML 요소)를 특정 위치에 삽입하는 고급 사용 예시를 보여줍니다. 이는 HTML 마크업 자체를 조각에 전달하고 활용할 수 있음을 의미합니다.


```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org/">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	
	<!-- fragment를 통해서 조각을 정의하는 것  -->
	
	<div th:fragment="frag01">
		<h4>Fragment</h4>
	</div>
	
	<div th:fragment="frag02(param01, param02)">
		<h4>Param</h4>
		<div th:text="${param01}"></div>
		<div th:text="${param02}"></div>
	</div>
	
	<div th:fragment="frag03(title)">
		<h4 th:replace="${title}">Param</h4>
		<h4>Param</h4>
	</div>
</body>
</html>
```

`util-object.html` : 여러 util 코드 예시를 담고 있는 파일


util-object.html은 Thymeleaf에서 제공하는 다양한 유틸리티 객체(#numbers, #strings, #dates 등)의 사용법을 보여주는 파일입니다. 이 파일도 basic.html에서 프래그먼트 형태로 호출되어 사용됩니다.

주요 내용:

- th:fragment="utilFragment": 이 파일 전체를 하나의 재사용 가능한 조각으로 정의합니다.

- 유틸리티 객체 목록: #numbers, #strings, #arrays, #lists, #dates 등 Thymeleaf에서 기본적으로 제공하는 유용한 내장 객체들을 소개합니다.

- #numbers: 숫자 포맷팅(정수/실수 자릿수, 통화, 퍼센트), 숫자 시퀀스 생성(sequence) 등 숫자 관련 유용한 함수들을 보여줍니다.

- #strings: 문자열 관련 다양한 함수들(비어있는지 확인, 기본값 설정, 포함 여부, 인덱스, 부분 문자열 추출, 치환, 길이, 비교, 결합, 분리 등)을 예시합니다.

- #dates: 날짜 및 시간 객체 생성, 포맷팅, 특정 날짜 구성 요소(년, 월, 일, 시, 분, 초) 추출 등 날짜 관련 함수들을 보여줍니다.



```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<div th:fragment="utilFragment">
	<div>
     #numbers, #strings, #arrays, #lists, #sets, #maps, #objects, #bools, 
     #dates, #calendars, #uris, #messages, #conversions, #aggregates, #ids
     <!-- /* 정수,실수 자리수를 스팩에 맞추어 설정한다. */ -->
	</div>
	<h3>#numbers</h3>
	<ul th:with="val=123456789.1359, val1=12.123, rate=${val1/100}">
		<li th:text="${#numbers.formatInteger(num, 3)}"></li>
		<li th:text="${#numbers.formatInteger(val, 3)}"></li>
		<li th:text="${#numbers.formatInteger(val1, 5)}"></li>
		<li th:text="${#numbers.formatDecimal(val, 3, 2)}"></li>
		<li th:text="${#numbers.formatDecimal(val, 0, 5)}"></li>
		<li th:text="${#numbers.formatDecimal(val, 0,'COMMA', 2,'POINT')}"></li>
		<li th:text="${#numbers.formatDecimal(val1, 5, 2,'POINT')}"></li>
		<li th:text="${#numbers.formatCurrency(val)}"></li>
		<li>[[${#numbers.formatCurrency(val)}]]</li>
		<li th:with="val2=${rate }" th:text="${#numbers.formatPercent(val2, 0, 1)}"></li>
		<li><span th:each="i: ${#numbers.sequence(1,10)}">[[${i }]],</span></li>
		<li><span th:each="i: ${#numbers.sequence(10,1,-1)}">[[${i }]],</span></li>
	</ul>
	
	<h3>#strings</h3>
	<ul th:with="val='abcde', val1='가,나,다,라,마', val2=null">
		<li th:text="${#strings.isEmpty(val2)}"></li>
		<li th:text="${#strings.defaultString(val2, 'hello')}"></li>
		<li th:text="${#strings.contains(val, 'a')}"></li>
		<li th:text="${#strings.indexOf(val, 'a')}"></li>
		<li th:text="${#strings.substring(val1, 1, 3)}"></li>
		<li th:text="${#strings.replace(val1, ',', ':')}"></li>
		<li th:text="${#strings.trim(val1)}"></li>
		<li th:text="${#strings.length(val1)}"></li>
		<li th:text="${#strings.equals(val, val1)}"></li>
		<li th:text="${#strings.append(val, val1)}"></li>
		<li th:with="strs=${#strings.arraySplit(val1, ',')}">
			<span th:each="str: ${strs }">[[${str }]]-</span><br>
			<span th:text="${#strings.arrayJoin(strs,'|')}"></span>
		</li>
		<li th:text="${#strings.toString(val2)}"></li>
	</ul>
	
	<h3>#dates</h3>
	<ul th:with="date=${#dates.create('2024', '01', '02', '15', '30', '50')}, date1=${#dates.createNow()}, date2=${#dates.createToday()}">
		<li th:text="${#dates.format(date)} "></li>
		<li th:text="${#dates.format(date, 'yyyy-MM-dd HH:mm:ss')} "></li>
		<li th:text="| ${#dates.day(date1)}, ${#dates.month(date1)}, ${#dates.year(date1)}, ${#dates.dayOfWeek(date1)}, 
		${#dates.hour(date1)}, ${#dates.minute(date1)}, ${#dates.second(date1)} |"></li>
	</ul>

</div>
</body>
</html>
```


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

**궁금했던 점**

```java
@Autowired
DeptService deptService; 
// 근데 인터페이스 DeptService, 구현 BasicDeptService로 했는데 왜 basicDeptService로 받지 않음?
// 그 이유는 자료형을 기반으로 자동을 찾아 주기 때문이다. 
```


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
