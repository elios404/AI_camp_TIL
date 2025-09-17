---
layout: post
title: "9월 16일 화요일 TIL(Docker)"
date: 2025-09-16 18:00:00 +0900
categories: July_week12
---

# 9월 16일 화요일 TIL 작성

## 1. 학습 주제
- Docker

## 2. 새롭게 알게된 점 / 핵심 개념 (가장 중요하다고 생각하는 개념)

### Docker 환경설정

window powershell 에서 `wsl(window subsystem  for lunux) install` 안되면 `wsl —update`

or `wsl —update-download`

- 

**WSL** → 윈도우 커널 위에 **리눅스 커널 계층을 얹어서** 리눅스 명령어나 프로그램만 실행할 수 있게 해주는 것.

- WSL1: 시스템 콜 번역(호환 계층) 방식 → 가볍지만 완벽하지 않았음.
- WSL2: 실제 **리눅스 커널**을 마이크로 VM 위에 올려서 더 완전한 리눅스 환경 제공 → 그래도 일반 VM보다 훨씬 가볍고 윈도우랑 통합이 잘 됨.

한 뒤에 

`docker —version` 으로 확인한 뒤에 

`docker run hello-world` 이걸로 실행 확인 밑에처럼 되면 맞음

`docker run` > Docker 실행하는 명령어 뒤에 이미지 붙이면 그 이미지를 실행시킴

```python
Run 'docker --help' for more information
PS C:\Windows\system32> docker --version
Docker version 28.3.0, build 38b7060
PS C:\Windows\system32> docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
17eec7bbc9d7: Pull complete
Digest: sha256:54e66cc1dd1fcb1c3c58bd8017914dbed8701e2d8c74d9262e26bd9cc1642d31
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

컨테이너 안에 하드웨어 장비에 대한 내요과 커널은 준비되어잇음 → 도커 엔진이 해줌 

도커 이미지만 넣어주면 됨 

$도커 이미지 안에는 $

실행할 파일

실행 될 명령어

### Docker ps 로 확인되는 내용

- **CONTAINER ID** : 컨테이너의 고유한 아이디 해쉬값
- **IMAGE** : 컨테이너 생성시 사용한 도커 이미지
- **COMMAND** : 컨테이너 시작시 실행될 명령어
- **CREATED** : 컨테이너 생성된 시간
- **STATUS** : 컨테이너 상태
- **PORTS** : 컨테이너가 개방한 포트와 호스트에 연결한 포트
- **NAMES** : 컨테이너의 고유한 이름(컨테이너 생성시 `-name` 옵션으로 이름을 설정하지 않으면 도커 엔진이 임의로 설정)

### Docker 명령어

`docker run` ⇒ Docker 실행하는 명령어 뒤에 이미지 붙이면 그 이미지를 실행시킴 

`docker ps` ⇒ 현재 돌아가고 있는 docker 엔진에 대한 내용 

`docker ps -a` ⇒ 과거 실행된 모든 도커엔진에 대한 내용을 볼 수 있음 

`docker stop` 컨테이너 아이디  ⇒  약간 delay (자원반납 등 마무리를 해줌) 하고 컨테이너 중지

`docker kill` 컨테이너 아이디 ⇒ 강종같은거 

`docker rm` 컨테이너 아이디 ⇒ 도커 컨테이너를 아예 삭제, 컨테이너 중지가 먼저 

`docker images` ⇒ 도커 이미지 리스트 보여줌

`docker rmi` 이미지 아이디 ⇒ 그 도커 이미지 삭제, 단 컨테이너가 먼저 삭제되어야만 삭제 가능 

- 삭제 흐름 running ⇒ stop ⇒ rm ⇒ rmi

`FOR /F %i IN ('docker ps -f "status=exited" -q') do docker rm %i` ⇒ 컨테이너 전체 삭제 

`docker image prune -a` ⇒ 이미지 전부 삭제

`docker system prune` ⇒ 캐싱 등 그냥 시스템 메모리 잡아먹는 삭제 

`docker exec` <컨테이너 아이디> <명령어> ⇒ 그 컨테이너에 들어가서 그 컨테이너 안에 있는 명령어 실행

`docker exec -it`  <컨테이너 아이디> <명령어> ⇒ it은 interact하게 명령어를 계속 실행하기 위해서 

`docker exec (-it) 프로세스ID 명령어`  : 도커의 컨테이너 안에, 실행 프로그램에 명령어 넣는 법, (-it) 인

### Docker 컨테이너 이미지 삭제

1. 프로세스 종료 `docker stop p_id`
2. 컨테이너 삭제 `FOR /F %i IN ('docker ps -f "status=exited" -q') do docker rm %i`
3. 이미지 삭제 `docker image prune -a`

### Dockerfile 과 build

Dockerfile은 프로젝트 폴더의 가장 root에 위치하게 한다.

`FROM` (base image) : 베이스 이미지

`RUN`

`CMD` [ ] : 실제 실행할 때 실행되는 명령어

`docker build 주소` 를 통해서 이미지를 만들 수 있다.

`docker build -t elios404/nodejs ./` -t를 통해서 이미지에 이름을 주고 (보통 도커허브ID/이름) Dockerfile이 있는 위치

### node.js + express.js → 도커로 실행

`npm init` → 의존성 설정 → `npm install` → `node index.js` 로 실행

`docker build -t elios404/nodejs ./` 이런 식으로 이미지 생성

`docker run -p 5000:8082 elios404/nodejs` 이런 식으로 도커로 웹 서버를 열 때 도커 내 포트와 내 컴퓨터의 포트를 매핑 해줘야 한다.

### 서로 다른 docker 컨테이너를 연결하기

**docker Compose** 를 통해서 다중 컨테이너 도커 애플리케이션을 정의

`docker-compose.yml` 파일을 사용한다. 내용을 채우기

`docker-compose build` → `docker-compose up`  / 혹은 `docker-compose up --build`  / `docker-compsoe up -d --build`  

compose를 한 번에 종료 `docker-compose down`

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
항상 이야기만 듣던 Docker에 대해서 처음 배워서 매우 흥미로웠고, 생각보다 어렵지 않았다. 공부를 열심히 해서 개발 뿐만 아니라 어떻게 배포하고 클라우드에 올리는 지도 잘 알고 싶다.