---
title: "쿠버네티스 YAML의 기본 구조"
date: 2026-04-24 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, yaml, api]
---

쿠버네티스는 선언형 시스템이다. 사용자는 원하는 상태를 YAML로 작성하고, API Server에 전달한다. 그래서 YAML 구조를 이해하는 것은 쿠버네티스를 다루는 기본이다.

## 쿠버네티스 리소스의 공통 구조

대부분의 쿠버네티스 YAML은 비슷한 구조를 가진다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: echo
  labels:
    app: echo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: echo
  template:
    metadata:
      labels:
        app: echo
    spec:
      containers:
        - name: echo
          image: nginx:latest
status: {}
```

실제로 사용자가 작성하는 부분은 주로 `apiVersion`, `kind`, `metadata`, `spec`이다. `status`는 쿠버네티스가 관리하는 읽기 전용 정보에 가깝다.

## apiVersion

`apiVersion`은 리소스가 어떤 API 그룹과 버전을 사용하는지 나타낸다. 예를 들어 Deployment는 보통 `apps/v1`을 사용하고, 기본 Service는 `v1`을 사용한다.

리소스 종류마다 사용하는 API 버전이 다르기 때문에 공식 문서나 `kubectl explain`으로 확인하는 습관이 필요하다.

## kind

`kind`는 만들 리소스의 종류다. Pod, Deployment, Service, Ingress, Job 같은 값이 들어간다.

같은 YAML 구조처럼 보여도 kind가 바뀌면 의미가 완전히 달라진다.

## metadata

`metadata`에는 리소스 이름, 라벨, 네임스페이스 같은 식별 정보가 들어간다. 라벨은 특히 중요하다. Service가 Pod를 찾거나 ReplicaSet이 Pod를 관리할 때 라벨 selector를 사용하기 때문이다.

## spec

`spec`은 사용자가 원하는 상태를 정의하는 부분이다. Deployment라면 replicas와 Pod template이 들어가고, Service라면 port와 selector가 들어간다.

즉, spec은 리소스마다 구조가 다르다. 쿠버네티스를 공부할 때는 각 리소스의 spec을 읽는 연습이 필요하다.

## 정리

쿠버네티스 YAML은 원하는 상태를 API Server에 전달하는 문서다. 공통적으로 apiVersion, kind, metadata, spec 구조를 가지며, status는 쿠버네티스가 관리한다. 이 구조를 알면 새로운 리소스를 봐도 어디를 읽어야 하는지 감이 잡힌다.
