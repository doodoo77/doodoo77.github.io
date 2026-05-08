---
title: "쿠버네티스 전체 흐름 다시 정리하기"
date: 2026-05-08 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, summary, cka]
---

지금까지 VM과 컨테이너, Docker, 오케스트레이션, 쿠버네티스 오브젝트, 네트워크, 저장소, HPA까지 정리했다. 마지막으로 전체 흐름을 하나로 묶어보자.

## 출발점은 컨테이너다

컨테이너는 애플리케이션과 실행 환경을 이미지로 묶는다. Dockerfile로 이미지를 만들고, Registry에 올리고, 필요한 환경에서 실행한다. 이 방식은 배포와 롤백을 단순하게 만든다.

하지만 컨테이너가 많아지면 운영 문제가 생긴다. 어디에 배치할지, 죽으면 어떻게 복구할지, 외부 요청은 어떻게 연결할지, 데이터는 어떻게 유지할지 고민해야 한다.

## 쿠버네티스는 원하는 상태를 맞춘다

쿠버네티스는 사용자가 선언한 원하는 상태를 기준으로 현재 상태를 맞춘다. Deployment에 replicas를 3으로 적으면, 실제 Pod가 3개가 되도록 계속 조정한다.

Control Plane은 API Server, etcd, Scheduler, Controller로 구성된다. API Server는 요청의 입구이고, etcd는 상태 저장소다. Scheduler는 Pod 위치를 정하고, Controller는 원하는 상태와 현재 상태를 계속 맞춘다.

Worker Node에서는 kubelet이 Pod를 실행하고, kube-proxy가 Service 네트워크 규칙을 구성한다.

## 애플리케이션 실행 구조

일반적인 웹 애플리케이션은 Deployment로 배포한다. Deployment는 내부적으로 ReplicaSet을 만들고, ReplicaSet은 Pod 수를 유지한다.

Pod는 직접 접근 대상이 아니라 Service 뒤에 둔다. Service는 Pod IP가 바뀌어도 안정적인 접근 지점을 제공한다. 외부 요청은 Ingress나 LoadBalancer를 통해 들어오고, 최종적으로 Service를 거쳐 Pod에 도달한다.

## 설정과 데이터

설정은 ConfigMap으로 분리할 수 있다. 애플리케이션 이미지를 다시 만들지 않고 환경별 설정을 바꿀 수 있다. 다만 설정 변경 후 애플리케이션이 자동으로 다시 읽는지는 별도로 확인해야 한다.

데이터는 Volume으로 다룬다. 임시 공유는 emptyDir을 사용할 수 있고, 영구 데이터는 PV, PVC, StorageClass를 통해 관리한다. Storage를 설계할 때는 Block, File, Object Storage의 차이와 accessModes를 함께 봐야 한다.

## 자동 확장

트래픽 변화에 대응하려면 HPA를 사용할 수 있다. HPA는 CPU 같은 지표를 보고 Deployment의 replicas 값을 조정한다. 이때 minReplicas와 maxReplicas로 범위를 제한하고, scaleDown 안정화 옵션으로 불필요한 축소를 줄일 수 있다.

## 마무리

쿠버네티스는 단순히 컨테이너를 실행하는 도구가 아니다. 컨테이너를 클러스터 위에서 배치하고, 복구하고, 연결하고, 확장하고, 데이터를 유지하게 만드는 운영 시스템이다. 각 오브젝트는 따로 보면 복잡하지만, 전체 흐름은 하나다. 원하는 상태를 선언하고, 쿠버네티스가 그 상태를 계속 맞춘다.
