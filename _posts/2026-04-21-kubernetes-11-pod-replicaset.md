---
title: "Pod와 ReplicaSet의 관계"
date: 2026-04-21 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, pod, replicaset]
---

쿠버네티스에서 가장 기본적인 실행 단위는 Pod다. 하지만 운영 환경에서 Pod를 하나씩 직접 관리하는 일은 드물다. Pod는 언제든 새로 만들어질 수 있고 사라질 수 있기 때문이다. 그래서 Pod 수를 유지하는 ReplicaSet이 필요하다.

## Pod는 실행 단위다

Pod는 하나 이상의 컨테이너를 묶은 단위다. 같은 Pod 안의 컨테이너들은 네트워크와 저장소 일부를 공유할 수 있다. 가장 흔한 형태는 하나의 Pod에 하나의 애플리케이션 컨테이너를 넣는 방식이다.

Pod는 IP를 가진다. 하지만 이 IP를 장기적으로 신뢰하면 안 된다. Pod는 장애, 업데이트, 스케일링 과정에서 쉽게 교체된다.

## ReplicaSet은 Pod 개수를 유지한다

ReplicaSet은 지정된 수의 Pod가 항상 유지되도록 관리한다. 예를 들어 replicas가 3이면, ReplicaSet은 라벨 selector에 맞는 Pod가 3개인지 계속 확인한다.

Pod가 2개만 있으면 하나를 새로 만들고, 4개가 있으면 하나를 줄인다. 이 구조 덕분에 애플리케이션 인스턴스 수를 안정적으로 유지할 수 있다.

## selector와 template

ReplicaSet에는 selector와 template이 중요하다. selector는 어떤 Pod를 자신이 관리할 대상으로 볼지 정하는 기준이다. template은 새 Pod를 만들 때 사용할 스펙이다.

예를 들어 `app=echo`, `tier=app` 라벨을 가진 Pod를 관리하도록 selector를 설정할 수 있다. Pod 수가 부족하면 template에 정의된 스펙으로 새 Pod를 만든다.

## ReplicaSet을 직접 쓰는 경우는 많지 않다

ReplicaSet 자체도 중요한 개념이지만, 실제로는 Deployment를 더 많이 사용한다. Deployment가 내부적으로 ReplicaSet을 만들고 관리하기 때문이다.

즉, 사용자는 Deployment를 선언하고, Deployment가 ReplicaSet을 통해 Pod 수를 유지한다. 그래서 ReplicaSet은 Deployment의 내부 동작을 이해하기 위한 핵심 개념으로 보는 것이 좋다.

## 정리

Pod는 컨테이너 실행 단위이고, ReplicaSet은 Pod 개수를 유지하는 컨트롤러다. 운영 환경에서는 Pod를 직접 관리하기보다 Deployment를 만들고, Deployment가 ReplicaSet을 통해 Pod를 관리하게 하는 방식이 일반적이다.
