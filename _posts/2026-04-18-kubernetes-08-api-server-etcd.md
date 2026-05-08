---
title: "API Server와 etcd를 같이 이해하기"
date: 2026-04-18 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, api-server, etcd]
---

쿠버네티스 Control Plane에서 가장 중심이 되는 구성 요소는 API Server와 etcd다. API Server는 요청의 입구이고, etcd는 상태의 저장소다. 두 개를 함께 이해하면 쿠버네티스의 동작 흐름이 훨씬 명확해진다.

## API Server는 모든 요청의 관문이다

kubectl 명령을 실행하면 요청은 API Server로 전달된다. 사용자가 Pod를 만들거나 Deployment를 수정하거나 Service를 조회할 때 모두 API Server를 거친다.

API Server는 단순 전달자만은 아니다. 요청을 검증하고, 권한을 확인하고, 필요한 경우 etcd에 상태를 저장한다. 쿠버네티스 내부 구성 요소들도 API Server를 통해 상태를 조회하거나 변경한다.

## etcd는 상태 저장소다

etcd는 쿠버네티스 클러스터의 상태를 저장하는 key-value 저장소다. 여기에는 사용자가 선언한 원하는 상태와 실제 클러스터 상태 정보가 저장된다.

예를 들어 Deployment의 replicas 값이 3이라면, 그 정보가 etcd에 저장된다. 이후 Controller가 현재 Pod 수를 확인하고 원하는 수와 다르면 조정한다.

## 왜 API Server만 etcd와 통신할까

쿠버네티스에서는 각 구성 요소가 etcd에 직접 접근하지 않고 API Server를 통해 통신한다. 이렇게 하면 인증, 권한, 검증, 기록을 한 곳에서 관리할 수 있다.

만약 모든 구성 요소가 etcd에 직접 접근한다면 상태 변경 경로가 복잡해지고, 일관성을 유지하기 어려워진다. API Server를 단일 진입점으로 두면 구조가 단순해진다.

## 요청 처리 흐름

Deployment를 하나 만든다고 가정하면 흐름은 다음과 같다.

1. 사용자가 kubectl로 요청을 보낸다.
2. API Server가 요청을 받는다.
3. API Server가 요청을 검증하고 권한을 확인한다.
4. 변경된 원하는 상태가 etcd에 저장된다.
5. Controller가 변경을 감지하고 필요한 작업을 시작한다.

## 정리

API Server는 쿠버네티스 요청의 입구이고, etcd는 클러스터 상태의 저장소다. 모든 상태 변경이 API Server를 통과하기 때문에 쿠버네티스는 클러스터 상태를 일관되게 관리할 수 있다.
