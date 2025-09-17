---
layout: post
title: "9월 17일 수요일 TIL(Docker)"
date: 2025-09-17 18:00:00 +0900
categories: July_week12
---

# 9월 17일 수요일 TIL 작성

## 1. 학습 주제
- Docker

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)
jar, war 차이 알아보기!

일단 내가 아는 건, jar는 자체로 서버를 가지고 있고, war는 외부 tomcat 서버가 필요하다.

### 스프링 프로젝트 build

`./mvnw package` : 로 jar 파일 생성하기

도커에 올릴때는 이 jar가 필요하고 jar로 서버 실행이 가능하다.

`java -jar .\target\spring_docker-0.0.1-SNAPSHOT.jar` 로 실행가능

만약 코드 수정이 발생했다면

`./mvnw clean` 으로 지우고 다시 jar 생성하기

#### Dockerfile

```docker
FROM open:jdk:17

COPY target/spring_docker-0.0.1-SNAPSHOT.jar app.jar

ENTRYPOINT ["java","-jar", "app.jar"]
```

copy 할 때 뒤에 다른 이름을 붙히고 사용할 수 있음.

`docker build -t elios404/spring_docker ./`     로 이미지 빌드 -t 뒤에 이미지 이름 붙이기

### maven docker plugin

https://dmp.fabric8.io/ 참고 웹페이지

pom.xml에 설정을 진행

`./mvnw docker:build` 을 통해서 docker 이미지 빌드 가능

추가 기능 확장

```xml
<!-- 빌드와 도커 이미지 생성, 실행 자동화(maven docker plugin) -->
			<plugin>
				<groupId>io.fabric8</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>0.46.0</version>
				<configuration>
					<images>
						<image>
							<name>elios404/springdocker</name> <!--docker의 이미지 이름-->
							<build>
								<dockerFileDir>${basedir}</dockerFileDir> <!--Dockerfile 주소-->
							</build>
						</image>
					</images>
				</configuration>
				  <executions>
					<execution>
					<id>docker-build</id>
					<phase>package</phase> <!-- package 단계에서-->
					<goals>
						<goal>build</goal> <!-- docker image build가 진행되도록-->
					</goals>
					</execution>
				</executions>
			</plugin>
```

이를 통해서 `./mvnw package` 를 하면 jar 파일 생성과 docker image 생성까지 자동으로 진행됨.

### docker hub에 올리기

`docker login -u elios404` 사용하고 password에 토근이나 실제 비밀번호 입력하기

`./mvnw docker:push` 로 푸쉬(이건 pom.xml에 플러그인 넣어서 그런듯?)

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
