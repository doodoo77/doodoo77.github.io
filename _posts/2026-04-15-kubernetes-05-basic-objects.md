---
title: "쿠버네티스 기본 오브젝트 한 번에 보기"
date: 2026-04-15 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, objects, pod]
---

쿠버네티스는 여러 종류의 오브젝트를 조합해서 애플리케이션을 실행한다. 처음에는 이름이 많아서 복잡해 보이지만, 역할을 기준으로 보면 흐름이 잡힌다.

## Node

Node는 서버 한 대를 의미한다. 실제 애플리케이션이 실행되는 물리 서버나 가상 서버가 Node가 된다. 쿠버네티스 클러스터는 여러 Node를 묶어 관리한다.

Node에는 kubelet, kube-proxy 같은 구성 요소가 동작한다. kubelet은 Pod 실행 상태를 관리하고, kube-proxy는 Service 트래픽이 적절한 Pod로 전달되도록 네트워크 규칙을 구성한다.

## Pod

Pod는 쿠버네티스에서 가장 작은 실행 단위다. 하나 이상의 컨테이너를 묶어 실행한다. 보통은 하나의 Pod에 하나의 애플리케이션 컨테이너를 넣지만, 로그 수집용 사이드카처럼 보조 컨테이너를 함께 넣는 경우도 있다.

Pod는 고유한 IP를 가진다. 다만 Pod는 언제든 새로 만들어질 수 있으므로, Pod IP를 직접 의존하는 구조는 좋지 않다.

## Deployment

Deployment는 애플리케이션 배포와 업데이트를 관리한다. Pod를 직접 하나씩 만들기보다 Deployment를 만들고, Deployment가 필요한 수의 Pod를 유지하게 하는 방식이 일반적이다.

Deployment는 내부적으로 ReplicaSet을 만든다. ReplicaSet은 지정된 수의 Pod가 유지되도록 관리한다.

## Service

Service는 Pod에 안정적으로 접근하기 위한 네트워크 진입점이다. Pod는 죽고 다시 만들어지면서 IP가 바뀔 수 있다. Service는 selector를 통해 특정 라벨을 가진 Pod들을 찾아 트래픽을 전달한다.

즉, 클라이언트는 Pod IP가 아니라 Service를 바라보면 된다.

## Ingress

Ingress는 외부 요청을 클러스터 내부 Service로 라우팅하는 규칙이다. 여러 서비스를 하나의 도메인이나 하나의 진입점 아래에서 나눠 연결할 수 있다. 실제 요청 처리는 Ingress Controller가 담당한다.

## Volume, PVC, PV, StorageClass

Pod 내부에 저장한 데이터는 Pod가 사라지면 함께 사라질 수 있다. 그래서 데이터를 유지하려면 외부 저장소와 연결해야 한다. 이때 사용하는 개념이 Volume, PVC, PV, StorageClass다.

PVC는 필요한 저장소 조건을 요청하는 리소스이고, PV는 실제 제공되는 저장 공간이다. StorageClass는 PVC 요청이 들어왔을 때 어떤 방식으로 저장소를 만들지 정하는 정책이다.

## 정리

Node 위에서 Pod가 실행되고, Deployment가 Pod 수와 배포를 관리한다. Service는 Pod 접근을 안정화하고, Ingress는 외부 요청을 내부 서비스로 연결한다. 데이터 유지는 Volume과 PV 계열 리소스가 담당한다.
