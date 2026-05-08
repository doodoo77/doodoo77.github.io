---
title: "Ingress는 외부 요청의 라우팅 규칙이다"
date: 2026-04-27 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, ingress, networking]
---

Ingress는 외부 요청을 클러스터 내부 Service로 연결하기 위한 라우팅 규칙이다. NodePort처럼 서비스마다 포트를 열지 않고, 하나의 진입점에서 여러 서비스를 나눠 연결할 수 있다.

## Ingress가 필요한 이유

서비스가 하나라면 NodePort나 LoadBalancer만으로도 외부 노출이 가능하다. 하지만 서비스가 여러 개가 되면 포트와 로드밸런서 관리가 복잡해진다.

예를 들어 `/api`는 API 서버로, `/admin`은 관리자 페이지로, `/`는 프론트엔드로 보내고 싶을 수 있다. 이런 규칙을 Ingress로 정의할 수 있다.

## Ingress와 Ingress Controller

Ingress는 규칙이고, 실제 트래픽 처리는 Ingress Controller가 한다. Nginx, HAProxy, Traefik, AWS ALB Controller 같은 구현체가 있다.

쿠버네티스에 Ingress 리소스만 만든다고 바로 외부 요청이 처리되는 것은 아니다. 반드시 Ingress Controller가 설치되어 있어야 한다.

Minikube 환경에서는 다음처럼 nginx ingress addon을 켤 수 있다.

```bash
minikube addons enable ingress
```

## Service와의 관계

Ingress는 직접 Pod로 요청을 보내지 않는다. 보통 Ingress는 Service를 대상으로 라우팅하고, Service가 다시 Pod로 트래픽을 전달한다.

흐름은 다음과 같다.

```text
외부 사용자 → Ingress Controller → Ingress 규칙 → Service → Pod
```

## ClusterIP와 함께 쓰는 방식

Ingress를 사용할 때 내부 Service는 ClusterIP 타입으로 두는 경우가 많다. 외부 노출은 Ingress Controller가 담당하고, Service는 클러스터 내부에서 Pod 접근을 안정화한다.

이렇게 역할을 나누면 구조가 깔끔해진다.

## 정리

Ingress는 외부 요청을 내부 Service로 라우팅하는 규칙이다. 실제 처리는 Ingress Controller가 담당한다. 여러 서비스를 하나의 진입점으로 관리하고 싶다면 Ingress를 이해해야 한다.
