---
layout: post
title: "7월 30일 수요일 TIL(MyBatis, Spring Boot Setting)"
date: 2025-06-01 18:00:00 +0900
categories: July_week5
---

# 7월 30일 수요일 TIL 작성

## 1. 학습 주제
- MyBatis
- Spring Boot 세팅

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

DAO는 보통 멤버변수와 get/set 만 존재하게 구성한다.

문제를 추상화 하면서 발견한 문제의 핵심 사항 → 인사이트

어떤 문제를 추상화 하고 결국 무엇을 해결해야 하는지, 핵심 사항을 생각해 보자.

데이터베이스에서는 각 행을 구분할 수 있는 값, 고유값, 기본키(PRIMARY KEY)가 반드시 필요하다.

selectKey를 사용할 수 도 있다 → 내가 구현한 UID 같이 번호를 통해서 기본키를 생성하는 방식이다.

### 파일 관리

기존에는 view나 설정파일 등을 `webapp/WEB-INF` 아래에 담아서 웹에서 접근하여 공유할 수 있도록 파일 관리를 했다. 그러나 최근에는 JAVA Resource 안의 src/main/resources 안에서 대부분 관리한다. 어처피 배포하거나 컴파일 하는 과정에서 src/main/java,resource 등은 하나의 target 폴더로 모이기 때문에 리소스 폴더 아래에 두어도 문제가 없다.

### MyBatis의 사용 가능 함수

- <if test=””> 를 통해서 조건에 따라 sql을 넣거나 안 넣을 수 있다.
- <choose>,<when>,<otherwise> : 들어오는 키의 조건에 따라서 sql문을 실행하도록. 자바의 switch 같은 것
- <where> : 여러 개 중 1개를 선택하는 것이 아니라, 조건에 맞는 것을 전부 선택하기 위해서 사용. 조건을 사용자가 원하는 것에 따라서 1개 혹은 여러 개 선택할 때 사용하면 좋을 듯 하다.
- <set> : 업데이트를 할 때 전체 값을 업데이트 하는 것이 아니라, 값이 있는 부분만 업데이트 하도록
- <trim> : sql문의 앞 뒤에 특정 키워드를 붙이거나, 제거할 때 사용
- <foreach> : sql 결과를 collection으로 만들 때 사용?

### MyBatis 구현 코드 관련
```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="app.labs.ex08.mybatis.hr.dao.IEmpRepository"> 
<!-- 인터페이스를 지정하면 자동으로 구현 객체를 만들어줌, 따라서 인터페이스를 상속 받아서 EmpRepository.java를 만들 필요가 없음-->
	
	<!-- 태그를 사용하여 sql 문을 작성한다. 실제 내부에 sql문을 구현, 입력 파라미터 값, 출력 값의 형태를 알려줘야 한다.-->
	<select id="getEmpCount" parameterType="int" resultType="int">
        SELECT COUNT(*) FROM EMPLOYEES
        <if test="deptId != null"> <!-- deptId 가 있을 때 와 없을 때를 하나의 sql로 해결할 수 있다. -->
             WHERE DEPARTMENT_ID = #{deptId}
        </if>
    </select>
    
    
    <!-- 수동 매핑 : 왜 수동 매핑을 해 주어야 할까?-->
    <resultMap id="empMap" type="app.labs.ex08.mybatis.hr.model.EmpVO">
        <result property="employeeId" column="EMPLOYEE_ID" />
        <result property="firstName" column="FIRST_NAME" />
        <result property="lastName" column="LAST_NAME" />
        <result property="email" column="EMAIL" />
        <result property="phoneNumber" column="PHONE_NUMBER" />
        <result property="hireDate" column="HIRE_DATE" />
        <result property="jobId" column="JOB_ID" />
        <result property="salary" column="SALARY" />
        <result property="commissionPct" column="COMMISSION_PCT" />
        <result property="managerId" column="MANAGER_ID" />
        <result property="departmentId" column="DEPARTMENT_ID" />
    </resultMap>

    <select id="getEmpList" resultMap="empMap"> <!-- 결과 값이 여러 개면 자동으로 resuleType을 리스트로 만들어서 반환 -->
        SELECT * FROM EMPLOYEES
    </select>
    
    <delete id="deleteEmp"> <!-- delete 는 알아서 리턴값이 정해짐, 정해줄 필요 X -> 삭제된 행 int로 반환, 근데 파라미터 타입 어디감? -->
        DELETE FROM EMPLOYEES 
        WHERE EMPLOYEE_ID=#{empId} AND EMAIL=#{email}
    </delete>
```

#### mapper namespace
개발자가 IEmpRepository라는 인터페이스만 정의하고, 이 XML 파일에 SQL 문을 작성해두면, MyBatis가 런타임에 이 인터페이스를 구현하는 프록시(Proxy) 객체를 자동으로 생성해줍니다.

개발자는 이 프록시 객체를 주입받아 인터페이스의 메서드를 호출하면, MyBatis가 내부적으로 XML에 매핑된 SQL을 실행하고 결과를 반환해줍니다. 이 덕분에 직접 EmpRepository.java 같은 구현 클래스를 만들 필요가 없어 생산성이 크게 향상됩니다.

#### SQL 문 작성 (<select>, <delete>)
개념 설명: <select>, <insert>, <update>, <delete>와 같은 태그들은 실제 실행될 SQL 쿼리를 정의하는 부분입니다. 각 태그는 SQL 작업의 종류를 나타냅니다.

MyBatis는 이 태그들 안에 SQL을 작성하고, id 속성으로 매퍼 인터페이스의 메서드 이름과 매핑시킵니다.

parameterType: SQL 문에 전달될 입력 파라미터의 타입을 명시합니다. 자바의 원시 타입(int, String 등)이나 VO/DTO 객체, Map 등이 될 수 있습니다.

resultType: 단일 결과가 반환될 경우 결과 값의 타입을 명시합니다. 원시 타입이나 VO/DTO 객체 등이 될 수 있습니다.

resultMap: 복잡한 결과를 매핑할 때 사용하며, 아래에서 자세히 설명합니다.

#### <resultMap id="empMap" type="app.labs.ex08.mybatis.hr.model.EmpVO"> 는 무엇을 하는 코드?

resultMap은 데이터베이스의 컬럼명과 자바 객체(여기서는 EmpVO)의 필드명(프로퍼티)이 다를 때 수동으로 매핑 규칙을 정의하는 곳이다.

id="empMap": 이 resultMap의 고유 ID. 다른 <select> 태그에서 이 매핑 규칙을 참조할 때 사용한다.

type="app.labs.ex08.mybatis.hr.model.EmpVO": 이 resultMap이 어떤 자바 VO/DTO 클래스와 매핑될지를 지정한다.

`<result property="employeeId" column="EMPLOYEE_ID" />`

property: EmpVO 객체의 필드 이름입니다.

column: 데이터베이스 테이블의 컬럼 이름입니다.

이렇게 명시적으로 매핑을 해주면, EMPLOYEE_ID 컬럼의 값이 EmpVO 객체의 employeeId 필드에 할당됩니다.

#### "수동 매핑 : 왜 수동 매핑을 해 주어야 할까?"

자동 매핑의 한계: MyBatis는 기본적으로 자동 매핑 기능을 제공한다. 즉, 데이터베이스 컬럼명과 자바 VO/DTO의 필드명이 완전히 동일하거나 (대소문자 무시), 스네이크 케이스(snake_case)와 카멜 케이스(camelCase) 규칙을 따를 경우(EMPLOYEE_ID -> employeeId) 자동으로 매핑을 시도합니다.

수동 매핑의 필요성:

1. 컬럼명과 필드명이 규칙에 맞지 않게 다를 경우: 예를 들어, DB 컬럼이 EMP_NUM인데 EmpVO의 필드가 id인 경우 등.

2. JOIN 등으로 여러 테이블에서 가져온 컬럼이 중복될 때: 예를 들어 ID 컬럼이 여러 테이블에 존재할 때 어떤 테이블의 ID를 가져올지 명확히 할 필요가 있을 때.

3. 복잡한 객체 관계 매핑 (1:N, N:1): 하나의 쿼리로 여러 테이블의 데이터를 가져와 복합적인 자바 객체(예: 사원 정보와 부서 정보를 한 객체에 담는 경우)를 만들 때 association이나 collection 태그와 함께 resultMap이 필수적입니다.

4. 읽기 명확성 및 성능 최적화: 명시적으로 매핑을 정의함으로써 코드의 가독성이 좋아지고, MyBatis가 런타임에 자동 매핑 규칙을 분석하는 오버헤드를 줄일 수 있습니다.


### 스프링 부트

자동설정, 내장 서버, 스타터 종속성을 통해서 초기 설정을 최소화 하고 빠르게 앱을 구축할 수 있다.

MSA? → 최종 목적은 함수 단위로 서버와 DB를 따로 두는 것? / 우리나라에서는 도메인 별로 나누는 정도

스프링 부트 프로젝트 설정 때 문제가 발생하면 [gradle.properties] 파일을 프로젝트 안에 설정하고

org.gradle.java.home=C:\\labs_lect\\jdk-17.0.15+6 를 안에 입력하고 gradle 재시작하고 sts 재시작하기 -> 즉 java 의 주소 알려주기

자바 리소스의 템플릿이 view가 들어가는 부분, static은 고정된 부분

logback.xml 에서 appender를 통해서 로그를 어디에 내보낼지 정할 수 있다. console, file, db 등을 사용할 수 있고 안의 값을 바꾸어서 사용할 수 있다.

JSP를 사용하지 않고 html에서 thymeleaf를 사용할 수 있다. 


## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
