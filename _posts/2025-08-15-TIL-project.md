---
layout: post
title: "8월 15일 금요일 TIL(Project)"
date: 2025-08-15 18:00:00 +0900
categories: July_week7
---

# 8월 15일 금요일 TIL 작성

## 1. 학습 주제
- 8월 미니프로젝트 백엔드 구현
  - 구현하면서 고민한 점

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### 프로젝트 주제

설명 가능한 대출 심사 모델을 통한 개인별 대출 평가 서비스를 웹으로 구현하기

AI 모델은 파이썬 언어를 기반으로 캐글에 있는 대출 평가 데이터를 사용, XGBoost로 모델을 만들고 각 요소별로 예측에 어떤 영향을 미쳤는지 확인하는 라이브러리를 통해서 설명 가능한 모델 만들기

백엔드 부분을 스프링을 통해서 구현하고 DB 는 MySQL를 사용, model, dao, dto, service, controller 로 이루어진 MVC 모델을 사용함. 

프론트 엔드는 html와 thymeleaf를 사용하여 서버사이드 렌더링을 사용.

### 폴더구조
```
src
├── main
│   ├── java
│   │   └── com.bank.bankservice
│   │       ├── loantest
│   │       │   ├── controller
│   │       │   │   ├── LoanController.java
│   │       │   │   ├── LoanTestController.java
│   │       │   │   └── UserController.java
│   │       │   ├── dao
│   │       │   │   ├── LoanRepository.java
│   │       │   │   ├── LoanTestRepository.java
│   │       │   │   └── UserRepository.java
│   │       │   ├── dto
│   │       │   │   └──LoanTest.java
│   │       │   ├── model
│   │       │   │   ├── Loan.java
│   │       │   │   └── User.java
│   │       │   └── service
│   │       │       ├── LoanService.java
│   │       │       ├── LoanServiceImpl.java
│   │       │       ├── LoanTestService.java
│   │       │       ├── LoanTestServiceImpl.java
│   │       │       ├── UserService.java
│   │       │       └── UserServiceImpl.java
│   │       └── BankServiceApplication.java
│   └── resources
│       ├── mappers
│       │   ├── LoanMapper.xml
│       │   ├── LoanTestMapper.xml
│       │   └── UserMapper.xml
│       ├── templates
│       │   ├── base
│       │   │   ├── default_layout.html
│       │   │   ├── home.html
│       │   │   ├── include.html
│       │   │   └── sample.html
│       │   ├── loantest
│       │   │   ├── details.html
│       │   │   ├── insert.html
│       │   │   └── list.html
│       │   └── user
│       │       ├── details.html
│       │       ├── insert.html
│       │       ├── list.html
│       │       └── update.html
│       ├── application.properties
│       └── logback.xml

```

### 구현 완료한 부분

현재 구현 완료한 부분은 user, loan 데이터를 폼을 통해서 입력 받고 DB에 저장하는 것, 전체 리스트를 불러와 확인하고, 각각의 세부 정보를 확인하는 페이지를 작성함.

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)

### 프로젝트 아키텍처 고민

restful API를 꼭 사용해야 할까?

나는 지금 HTML과 바닐라 js를 이용하고자 한다. 그리고 클릭이 발생하거나, 입력, 자세한 정보 출력을 할 때, 새로운 HTML을 만들어 보여주고자 한다. 이 경우  RESTful API 가 필요한가? 

→ 일단 이 구조에서는 필요하지 않다. 전통적인 MVC 구조로 작성하는데 더 간단한 수 있다. 어처피 HTML 자체가 바뀌기 때문에.

→ 하지만 현대적인 아키텍쳐 구조는 RESTful API로 구성하는 것이 더 좋다. React의 경우 back에서 html을 구성하지 않기 때문에 무조건 json으로 데이터를 보내 주어야 하고, 컴포넌트 위주이기에 이것은 더 중요하다.

따라서 

1. 먼저 MVC 구조로 구현해보기
2. 이후 React를 배워서, 그것에 맞춰서 RESTful API 방식으로 바꾸어보기

### loanTest 라는 새로운 dto, service, controller 생성

실제 대출 평가하는 서비스는 user에 대한 정보와  loan 에 담긴 금융 정보가 둘 다 필요하다. 처음에는 service와 controller 만 만들어서 각각 유저의 서비스나 loan의 서비스, 레포를 사용하고자 했다. 그러나 sql에서 조인을 하고 그것을 새로운 객체로 받는 것이 조회하기에 더 편리하다고 생각을 했고, 또한 loantest에 대한 객체와 서비스, 컨트롤러를 따로 만드는 것이 단일 책임 원칙에 부합한다고 생각하여 추가하였다.

## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
아직 백엔드 부분은 기능 구현이 폼을 통한 입력과 DB 정보 읽기 밖에 없어서 그런지, 백엔드 부분은 시간이 많이 소모되지 않았지만
프론트엔드부분은 많은 시간이 소모되었다.

그리고 확실히 fragment의 유용함을 느낄 수 있었다. React의 좋은 점을 알간 알 것 같기도..?

현재는 프론트를 html 과 thymeleaf를 통해서 구현했는데, 추후 react로 바꿔야 할지 고민중이다.