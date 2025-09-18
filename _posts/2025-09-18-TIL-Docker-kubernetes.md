---
layout: post
title: "9월 18일 목요일 TIL(Docker, Kubernetes)"
date: 2025-09-18 18:00:00 +0900
categories: July_week12
---

# 9월 18일 목요일 TIL 작성

## 1. 학습 주제
- kubernetes

## 2. 새롭게 알게된 점 / 핵심 개념 (중요하다고 생각하는 개념)
`kebectl version`  : 클러스터 버전 확인 (아래 결과)

```
Client Version: v1.32.2
Kustomize Version: v5.5.0
Server Version: v1.32.2
```

설치는 docker desktop 에서 setting에서 설치

### k8s - pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod 
spec:
  containers:
    - name: nginx-container
      image: nginx
      ports:
        - containerPort: 80
```

`kubectl apply -f nginx-pod.yaml` 로 yaml 파일을 실행?

`kubectl get pods` 를 통해서 돌아가는 pod을 확인가능

`kubectl exec -it nginx-pod -- bash` 를 통해서 pod안에 들어가기 가능

`kubectl port-forward pod/nginx-pod 80:80` 외부 port 와 매핑하는 법

`curl localhost:80` 으로 cmd에서 확인 혹은

브라우저에서 localhost:80으로 접근 가능

`kubectl delete pod nginx-pod` 으로 pod 삭제 가능

### k8s - deployment

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: spring-deployment

spec:
  replicas: 3
  selector:
    matchLabels:
      app: backend-app

  template:
    metadata:
      labels:
        app: backend-app
    spec:
      containers:
        - name: spring-container
          image: spring-server
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 8081
```

`kubectl apply -f spring-deployment.yaml`  로 만들기

`kubectl get deployment` 로 deployment 확인

`kubectl get replicaset` 로 replicaset 확인

`kubectl get pods` 로 pod 확인하면 replicas: 3 에 맞춘 3개 나온것을 확인 가능

### k8s - service

```yaml
apiVersion: v1 
kind: Service

metadata:
  name: spring-service

spec: 
  type: NodePort
  selector:
    app: backend-app
  ports:
    - protocol: TCP
      port: 8081
      targetPort: 8081
      nodePort: 30000
```

`kubectl apply -f spring-service.yaml` 로 적용

`kubectl get service` 로 확인

## 3. 문제 해결 경험 (학습과정에서 직면했던 문제와 에러, 이를 어떻게 해결했는가)


## 4. 참고 코드 / 예시


## 5. 참고자료 / 링크


## 6. 느낀 점 / 회고 
왜 대용량 트래픽에 대해서 이야기 할 때 쿠버네티스를 언급하는지 이해할 수 있었다. 서버 컨테이너를 여러 pod으로 올리고 그것을 kubernetes가 자동으로 관리해주는 부분이 사용자의 접근이 많아지고 서버에 부하가 걸릴 때 관리하는데 도움이 될 것이라고 생각했다.