---
layout: post
title: "7월 24일 목요일 TIL(Database)"
date: 2025-07-24 18:00:00 +0900
categories: July_week4
---

# 7월 24일 목요일 TIL 작성

## 1. 학습 주제
- DBMS
- DB 용어 정리
- DBMS, 서버간 SQL 실행 과정
- (Prepared)Statement
- MVC 패턴을 이용한 설계

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### DBMS

- SQL 를 통해서 조작
- 데이터가 변경되거나 깨지지 않도록 보안
- 여러 사람이 데이터를 변경할 때 데이터의 무결성을 보장

### DB 용어 정리 

- column : 한 특징을 가진 테이블 세로 줄, 특정 종류의 데이터(예: 이름, 나이, 주소 등)를 나타내는 세로 방향의 데이터 집합
- row : 컬럼들의 모음, 테이블의 가로 줄, 하나의 완전한 레코드(record) 또는 엔티티(entity)를 나타내는 가로 방향의 데이터 집합
- table : column 과 row의 모음, `column`으로 정의된 구조에 `row`들이 채워진 형태
- database : 여러 table들의 집합, 인덱스(Index), 뷰(View), 스토어드 프로시저(Stored Procedure) 등 다양한 데이터베이스 객체들을 포함하는 전체적인 저장소 및 관리 시스템
- cursor : 메모리에 올라온 임시 테이블, **SQL 쿼리 실행 결과로 반환된 행 집합(Result Set)에 접근하기 위한 포인터 또는 메커니즘.**

### DBMS에 SQL 문이 보내지면 실행되는 과정

연결을 매번 설정하고 끝는 것은 오버로딩이 될 수 있음. 미리 연결을 만들어 놓고, 그것이 필요할 때 마다 할당하는 방식으로 사용할 수 있음. 스프링에서는 bean으로 설정하면 자동으로 된다?(이 부분 확인 필요)

롬복을 사용하지 않으면 도메인 객체를 만들 때 수정에 따라서 get/set, toString을 매번 수정해야한다.

[**app.labs.ex05.jdbc01.EmpRepository.java**](http://app.labs.ex05.jdbc01.EmpRepository.java) 를 꼭 확인하고 실제 SQL을 jdbcTemplate 을 통해서 어떻게 적용시킬 수 있는지 꼭 확인하기

---

DB와 서버간의 SQL 전달

1. 구문(문법) 분석 : 문법에 맞는지
2. Table, column, object의 유무 검사, 실제로 그 테이블, 컬럼, 등이 있는지 확인
3. 실행 계획을 세우기(가장 빠른 실행 과정을 결정) → 더 DB를 잘 하고 싶으면 공부! 속도 향상을 위한 설계에 도움이 될 수 있다.

매 번 실행할 때 마다, 체크하는데 시간이 소모된다.

### preparedStatement

Statement는 기존에 호출된 쿼리를 캐싱(Cashing)하고 있다가 동일한 정적인 쿼리가 호출되는 경우 캐쉬에 저장된 것을 실행한다.

PreparedStatement 는 조건 값을 쿼리에서 분리하여 캐쉬를 생성하여 동적인 경우에도 캐쉬 기능을 사용할 수 있게 한다. 즉where 부분을 분리가능해 지고 (select * from ‘table 이름’)는 캐시에 저장하게 할 수 있는 기능이다.

#### `Statement` 와 `PreparedStatement` 용어 정리 검토

1. **`Statement`**:
"Statement는 기존에 호출된 쿼리를 캐싱(Cashing)하고 있다가 동일한 정적인 쿼리가 호출되는 경우 캐쉬에 저장된 것을 실행한다."
    - **부분적으로 맞습니다.** Statement 객체 자체는 클라이언트 측에서 쿼리 문자열을 그대로 데이터베이스로 보냅니다. Statement를 이용한 **쿼리의 캐싱은 데이터베이스 서버 측에서 수행**됩니다.
    - **재사용성:** Statement는 SQL 쿼리 문자열을 매번 새로 생성해서 DB로 보내기 때문에, DB 서버는 **매번 SQL 구문을 파싱하고 최적화(Execution Plan 생성)**하는 과정을 거칩니다. 만약 *완전히 동일한 쿼리 문자열*이 반복해서 들어오면, DB 서버의 내부 캐시(SQL Shared Pool 등)에서 이전에 파싱하고 최적화했던 Execution Plan을 재사용할 수는 있습니다. 즉, 캐싱은 Statement 객체가 하는 것이 아니라 **DB 서버가 쿼리 문자열을 기반으로 하는 것**입니다.
2. **`PreparedStatement`**:
"PreparedStatement 는 조건 값을 쿼리에서 분리하여 캐쉬를 생성하여 동적인 경우에도 캐쉬 기능을 사용할 수 있게 한다. 즉where 부분을 분리가능해 지고 (select * from ‘table 이름’)는 캐시에 저장하게 할 수 있는 기능이다."
    - **매우 정확합니다!** PreparedStatement의 가장 큰 장점을 잘 설명해 주셨습니다.
    - **미리 컴파일:** PreparedStatement는 SQL 쿼리에 **'?' (플레이스홀더)**를 사용하여 조건 값을 비워둔 채로 데이터베이스에 먼저 보냅니다. 데이터베이스는 이 미완성된 SQL 문을 한 번만 **파싱하고 컴파일(Execution Plan 생성)**하여 캐시(SQL Shared Pool 등)에 저장해 둡니다.
    - **성능 향상:** 이후에 실제 값을 이 '?'에 바인딩(bind)하여 여러 번 실행해도, 데이터베이스는 이미 캐시된 Execution Plan을 재사용하므로, 매번 쿼리를 파싱하고 최적화하는 오버헤드를 줄일 수 있습니다. 이는 특히 반복적으로 실행되는 동적인 쿼리(조건 값만 바뀌는 쿼리)에서 성능 향상에 크게 기여합니다.
    - **보안 (SQL Injection 방지):** PreparedStatement는 입력된 값을 SQL 문자열 자체에 삽입하는 것이 아니라, 별도의 '바인딩 변수'로 처리합니다. 이는 악의적인 SQL Injection 공격을 방지하는 강력한 보안 기능을 제공합니다. 입력된 문자열이 SQL 코드의 일부로 해석되지 않고, 순수한 '값'으로만 인식되기 때문입니다.

### 정리 및 추가 설명

| 특징 | `Statement` | `PreparedStatement` |
| --- | --- | --- |
| **SQL 형태** | 완성된 SQL 문자열을 직접 작성 | `?` (플레이스홀더)를 사용하여 동적인 값 삽입 |
| **SQL 파싱/컴파일** | 매번 SQL 문자열을 서버로 보내고 파싱/컴파일 (동일 쿼리 반복 시 DB 캐시 사용 가능) | SQL 구조만 미리 서버로 보내 한 번만 파싱/컴파일하여 캐시 |
| **성능** | 동적인 쿼리 반복 시 성능 저하 가능 | 동적인 쿼리 반복 시 성능 우수 (캐시 재사용) |
| **보안** | **SQL Injection 취약** (입력값에 따라 SQL 구조 변경 가능) | **SQL Injection 방지** (입력값이 값으로만 처리됨) |
| **용도** | 정적이고 한 번만 실행되는 쿼리 | 반복 실행되는 동적인 쿼리, 보안이 중요한 쿼리 |

**결론적으로, `PreparedStatement`는 성능과 보안 측면에서 `Statement`보다 훨씬 우수하며, 실제 개발에서는 대부분 `PreparedStatement`를 사용합니다.**

"where 부분을 분리가능해 지고 (select * from ‘table 이름’)는 캐시에 저장하게 할 수 있는 기능이다." 라는 설명은 `PreparedStatement`의 동작 방식을 아주 명확하게 비유적으로 표현하신 것입니다.


### MVC 패턴을 이용한 설계

프로젝트 폴더는  springFw 프로젝트의 jdbc01 패키지.

프로젝트의 구성을 DB-VO(value object)-Repository-Service-Controller-View(main) 와 같은 흐름으로 구성함

- DB : 리소스 폴더의 application-config에서 DB의 이름과 주소, 사용자와 암호 등을 통해서 DB에 연결, JDBCTemplate을 이용해서 연결 생성을 자동으로 진행, 스프링의 bean component-scan 등을 설정, transactionManager 등을 통해서 설정을 한다.
- VO : DAO와 DTO의 역할이 합쳐져 있다. 추후에는 나누어서 작성 가능. DB 의 각 열에 대해서 값을 받을 멤버 변수를 정의한다. 또한 롬복을 이용해서 getter/setter, 생성자, toString 등을 자동으로 생성해 줄 수 있다. 수정사항도 바로 적용
- Repository : 인터페이스에서는 사용할 메서드와 어떤 값을 입력 받을지, 반환 타입은 어떻게 될지 작성한다. 그리고 실제 구현 클래스에서 String으로 sql 문을 넘겨주고  jdbcTemplate.query 등을 사용한 매서드를 통해서 DB에 SQL 쿼리를 보내고 반환값을 받는다. 이떄 **Mapper** 메서드를 구현하는 것으로 한 행에 대해서 여러 열로 나오는 것을 받아서 VO나 DAO 방식으로 변환해 줄 수 있다.
- Service : 서비스에서는 Repository에 구현된 메서드 등을 포함하여 실제 서비스 로직을 작성한다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
