---
title: "Service와 ClusterIP로 내부 통신 안정화하기"
date: 2026-04-25 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, service, clusterip]
---

Pod는 언제든 새로 만들어질 수 있다. 이때 Pod IP도 바뀔 수 있다. 그래서 다른 애플리케이션이 Pod IP를 직접 바라보게 하면 문제가 생긴다. 쿠버네티스는 이 문제를 Service로 해결한다.

## Service가 필요한 이유

예를 들어 counter 애플리케이션이 redis 데이터베이스에 접근해야 한다고 하자. redis Pod의 IP를 직접 설정하면, redis Pod가 재시작될 때마다 IP가 바뀌어서 연결이 깨질 수 있다.

Service는 고정된 이름과 가상 IP를 제공한다. counter 애플리케이션은 redis Pod가 아니라 redis Service를 바라보면 된다.

## ClusterIP

ClusterIP는 Service의 기본 타입이다. 클러스터 내부에서만 접근 가능한 IP를 제공한다. 내부 서비스끼리 통신할 때 가장 흔하게 사용한다.

예를 들어 redis Service를 만들고 port 6379로 열어두면, 같은 클러스터 안의 애플리케이션은 redis Service를 통해 redis Pod에 접근할 수 있다.

## selector로 Pod를 찾는다

Service는 selector를 통해 대상 Pod를 찾는다. 예를 들어 selector가 `app=redis`라면, 해당 라벨을 가진 Pod들이 Service의 백엔드가 된다.

Pod가 새로 만들어져도 라벨만 맞으면 Service 뒤에 자동으로 연결된다. 이 구조 덕분에 Pod IP 변경을 직접 관리하지 않아도 된다.

## 트래픽 전달 흐름

내부 애플리케이션이 Service 주소로 요청을 보낸다. 그러면 쿠버네티스 네트워크 규칙에 따라 요청이 selector에 맞는 Pod 중 하나로 전달된다.

사용자는 Service만 바라보고, 실제 Pod 목록은 쿠버네티스가 관리한다.

## 정리

Service는 변동성이 큰 Pod 앞에 안정적인 접근 지점을 제공한다. ClusterIP는 클러스터 내부 통신에 사용하는 기본 Service 타입이다. Pod를 직접 연결하지 않고 Service를 통해 연결하면 애플리케이션 구조가 훨씬 안정적이다.
