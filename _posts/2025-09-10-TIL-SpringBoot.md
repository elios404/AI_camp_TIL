---
layout: post
title: "9월 10일 수요일 TIL(SpringBoot)"
date: 2025-09-10 18:00:00 +0900
categories: July_week11
---

# 9월 10일 수요일 TIL 작성

## 1. 학습 주제
- HTTP
- RESTful API
  - ResponseEntity
  - ResposneBody
- Spring boot
  - ResController


## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### HTTP

get : url로 정보 전달

post : body로 정보 전달

Stateful / Stateless

### RESTful API

url에 db 에 필요한 sql 쿼리의 정보를 담아서 보내는 것이다.

서버에서는 더이상 뷰를 만들 필요가 없고, 데이터를 가공해서 json으로 보내준다.

따라서 서버 디자인 할 때 RESTful 형식으로 하거나, JSP 형태로(오래된 프로젝트)

#### **Restful API에서 ResponseEntity를 사용하는 이유**

**1. 상태 코드(Status Code) 제어**`ResponseEntity`를 사용하면 HTTP 요청의 성공, 실패, 리소스 없음 등 다양한 상황에 맞는 정확한 상태 코드를 반환할 수 있습니다. 예를 들어, `200 OK`, `201 Created`, `400 Bad Request`, `404 Not Found` 등을 명시적으로 보낼 수 있습니다.

```java
// 성공적으로 생성된 경우 201 Created 상태 코드를 반환
return new ResponseEntity<>("리소스가 성공적으로 생성되었습니다.", HttpStatus.CREATED);

// 리소스를 찾을 수 없는 경우 404 Not Found 상태 코드를 반환
return new ResponseEntity<>(HttpStatus.NOT_FOUND);
```

**2. 응답 헤더(Header) 추가**
응답에 `Content-Type`, `Location`, `Authorization` 등 필요한 헤더를 추가할 수 있습니다. 예를 들어, 새로운 리소스가 생성되었을 때 해당 리소스의 URL을 `Location` 헤더에 담아 보낼 수 있습니다.

**3. 응답 본문(Body) 커스터마이징**
단순 `String`뿐만 아니라 Java 객체(예: DTO, Map)를 JSON 형식으로 변환하여 응답 본문에 담을 수 있습니다.

`String`을 반환하면 스프링은 기본적으로 HTTP 상태 코드를 **`200 OK`**로 설정합니다. 하지만 모든 상황이 `200 OK`는 아니므로, `ResponseEntity`를 사용해 API 클라이언트에게 더 명확하고 정확한 정보를 제공하는 것이 RESTful API 설계 원칙에 부합합니다.

### HTTP put/fetch 차이

put : 리소스를 새로운 것으로 완전히 대체

fetch : 일부만 수정 가능

#### **HTTP PUT**

- **개념**: HTTP PUT은 HTTP **메서드(Method)** 중 하나로, 서버의 **리소스(Resource)를 완전히 덮어쓸 때** 사용합니다.
- **사용 목적**: 기존 리소스의 모든 내용을 요청 본문(request body)에 담긴 새로운 내용으로 **교체(replace)**합니다.
- **멱등성(Idempotency)**: PUT 메서드는 **멱등적(idempotent)**입니다. 즉, 동일한 PUT 요청을 여러 번 보내도 서버의 상태는 한 번 보낸 것과 동일하게 유지됩니다.

#### **Fetch**

- **개념**: Fetch는 브라우저 환경에서 서버에 네트워크 요청을 보내기 위해 사용되는 **웹 API**입니다.
- **사용 목적**: JavaScript 코드를 사용하여 HTTP 요청(예: `GET`, `POST`, `PUT`, `PATCH`, `DELETE` 등)을 비동기적으로 보낼 때 사용합니다.
- **관계**: Fetch API를 사용하면 **PUT 메서드**를 포함하여 원하는 HTTP 요청을 보낼 수 있습니다.

### HTTP 상태코드

2xx(Successful) : 요청 정상처리

3xx(Redirection) : 요청을 완료하려면 추가 행동이 필요

4xx(Client Error) : 클라이언트 오류

5xx(Server Error) : 서버 오류

### @RequestBody

`@RequestBody`는 스프링(Spring) 프레임워크에서 HTTP 요청의 **본문(body)에 담긴 데이터를 자바 객체로 변환해주는 역할을 하는 어노테이션**입니다.

---

#### **`@RequestBody`의 작동 방식**

웹 클라이언트(예: 웹 브라우저, 모바일 앱)가 서버로 데이터를 보낼 때, 주로 JSON이나 XML 형식으로 데이터를 본문에 담아 보냅니다. 이때 `@RequestBody`는 이 본문의 데이터를 읽어와서 개발자가 지정한 자바 객체(DTO, VO 등)로 자동으로 변환해줍니다. 이 과정을 **역직렬화(Deserialization)**라고 합니다.

예를 들어, 웹 클라이언트가 다음과 같은 JSON 데이터를 서버로 보냈다고 가정해 보겠습니다.

```json
{
  "name": "홍길동",
  "email": "gildong@example.com"
}
```

스프링 컨트롤러에서 `@RequestBody`를 사용하면, 이 JSON 데이터가 `User`라는 자바 객체로 자동으로 변환되어 메서드의 매개변수로 전달됩니다.

```java
public class User {
    private String name;
    private String email;
    // getter와 setter
}

@PostMapping("/users")
public ResponseEntity<String> createUser(@RequestBody User user) {
    // 이제 'user' 객체 안에는 "홍길동"과 "gildong@example.com"이 담겨 있습니다.
    System.out.println("User name: " + user.getName());
    return ResponseEntity.ok("User created successfully!");
}
```

**`@RequestParam`, `@PathVariable`과 비교**

`@RequestBody`는 요청 본문을 다루는 반면, 다른 어노테이션들은 HTTP 요청의 다른 부분을 다룹니다.

- **`@RequestParam`**: URL의 **쿼리 파라미터**(예: `/users?id=123`)를 자바 변수로 매핑합니다.
- **`@PathVariable`**: URL 경로의 **일부**(예: `/users/123`)를 변수로 매핑합니다.

따라서 `@RequestBody`는 주로 `POST`나 `PUT` 요청처럼 클라이언트가 서버에 대량의 데이터를 보낼 때 유용하게 사용됩니다.

### @RestController

이건 객체를 json으로 바꿔서 클라이언트로 보내주는는 기능도 한다.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
