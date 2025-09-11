---
layout: post
title: "9월 11일 목요일 TIL(Spring boot, MyBatis)"
date: 2025-09-11 18:00:00 +0900
categories: July_week11
---

# 9월 11일 목요일 TIL 작성

## 1. 학습 주제
- Java
- Spring boot
- Swagger
- DB
  - Oracle
  - MyBatis

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
### 자바에서 왜 상속을 하는가?

기존의 기능을 유지하면서 새로운 기능을 추가하는 것으로 확장하기 위해서, 즉 **확장성**을 위해서

인터페이스의 활용은, 필요한 메서드를 정의해 놓고 사용하기 위해서 + 자바는 다중 상속이 안 되기에 여러 클래스에서 동시에 상속 받기 위해서는 인터페이스로 구성해서 여러 인터페이스를 상속받도록

인터페이스를 통해서 다형성을 구현할 수 있고, 코드의 일관성과 독립성이 유지된다.

### 스프링 유효성 검사

```xml
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
		<!-- 입력 값의 유효성을 평가하기 위한 의존성 JSR-303, domain에 적용 후 Controller에서 데이터가 넘어올때 사용 -->
		<dependency>
			<groupId>org.hibernate.validator</groupId>
			<artifactId>hibernate-validator</artifactId>
			<version>8.0.2.Final</version>
		</dependency>
```

이를 통해서

```java
// Domain 부분
public class User {
    private Integer id;
    @Size(min = 2, message = "Name should be at least 2 charaters")
    private String name;
    @Past(message = "joinDate can't be in the past")
    private Date joinDate;
}

// Controller 부분
@PostMapping("/users")
    public ResponseEntity<User> createUser(@Valid @RequestBody User user) { //클라이언트에서 오는 json을 객체로 변환 + @Valid를 통해서 유효성 체크
        User savedUser = service.save(user);
        URI location = ServletUriComponentsBuilder.fromCurrentRequest().path("/{id}").buildAndExpand(savedUser.getId()).toUri(); //현재의  uri 경로를 기반으로 새로운 uri 경로를 만들어줌.
        
        return ResponseEntity.created(location).build(); //created를 통해서 201로 상태코드 리턴
    }
```

이런 식으로 유효성 검사를 할 수 있다.

### Swagger 문서화

[`http://localhost:8081/swagger-ui/index.html`](http://localhost:8081/swagger-ui/index.html)

```xml
<!-- https://mvnrepository.com/artifact/org.springdoc/springdoc-openapi-starter-webmvc-ui -->
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.8.6</version>
</dependency>
```

이를 통해서 만든 백엔드 controller 를 문서화 가능

### Oracle 새로운 DB만들기 위한 계정 생성

```sql
CREATE USER agent IDENTIFIED BY "1234"; # "사용할 비밀번호", agent 쪽에 사용자 이름

GRANT CONNECT, RESOURCE TO agent;
```

### application.yml 설정하기(oracle, mybatis)

```yaml
spring:
    datasource:
        driver-class-name: oracle.jdbc.driver.OracleDriver
        url : jdbc:oracle:thin:@localhost:1521:xe
        username : agent
        password : 1234

mybatis:
    mapper-locations: classpath:mapper/*.xml #mapper 폴더 안에 모든 xml 파일들
    type-aliases-package: com.example.rest.domain #xml 안에서 결과를 매핑하는 객체들을 사용하기 위해서
```

### ResultMap을 Mapper에서 사용하기

```xml
<resultMap id="userMap" type="User">
        <id property="id" column="u_id"/>
        <result property="name" column="name"/>
        <result property="joinDate" column="joinDate"/>
        <collection property="posts" resultMap="postMap"/>
    </resultMap>

    <resultMap id="postMap" type="Post">
        <result property="id" column="p_id"/>
        <result property="description" column="description"/>
        <result property="user_id" column="user_id"/>
    </resultMap>

    <select id="findAllUsers" resultType="User" resultMap="userMap">
        select
            u.id as u_id, name, joinDate, p.id as p_id , description, user_id
        from
            users u LEFT OUTER JOIN post p
        ON u.id = p.user_id
    </select>
```

### 동적 sql mapper 만들기

```xml
<!-- where (title LIKE %JSP% OR writer LIKE %JSP%) -->
	<!-- where (title LIKE %JSP%) -->
	<!-- where (writer LIKE %JSP%) -->
	<select id="listBoard" parameterType="java.util.Map" resultType="Board">
		select * from board
		<if test="area != null">
			<where>
				<foreach collection="area" item="item" open="(" close=")" separator="OR">
					${item} LIKE #{searchKey} 
				</foreach>
			</where>
		</if>
		 order by seq desc
	</select>
```

foreach는 area(예를 들어 리스트) 안에 item을 하나씩 순회하면서 반복.

open을 통해서 시작하고, close를 통해서 닫음. seperator를 통해서 반복문 중간에 특정 문자열을 넣을 수 있음.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
