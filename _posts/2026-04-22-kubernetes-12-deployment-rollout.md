---
title: "Deployment와 롤링 업데이트 이해하기"
date: 2026-04-22 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, deployment, rollout]
---

Deployment는 쿠버네티스에서 애플리케이션을 배포할 때 가장 자주 사용하는 오브젝트다. 단순히 Pod를 만드는 것이 아니라, 버전 업데이트와 롤백까지 관리한다.

## Deployment가 하는 일

Deployment는 원하는 Pod 개수와 Pod 템플릿을 선언한다. 그러면 Deployment는 내부적으로 ReplicaSet을 만들고, ReplicaSet이 실제 Pod 수를 유지한다.

사용자는 `replicas: 4`처럼 원하는 개수를 적고, 어떤 컨테이너 이미지를 사용할지 정의한다. 쿠버네티스는 그 상태를 맞추기 위해 필요한 Pod를 만든다.

## 이미지 버전이 바뀌면 어떻게 될까

예를 들어 기존 이미지가 `echo:v1`이고 새 이미지가 `echo:v2`라고 하자. Deployment의 image 값을 v2로 바꾸면 쿠버네티스는 새 버전의 Pod를 점진적으로 만든다.

동시에 기존 v1 Pod는 점진적으로 줄어든다. 이 과정을 롤링 업데이트라고 한다. 한 번에 모든 Pod를 내리고 다시 올리는 방식보다 안정적이다.

## ReplicaSet이 버전을 나눠 관리한다

Deployment는 버전별로 ReplicaSet을 관리한다. v1을 위한 ReplicaSet이 있고, v2 배포가 시작되면 v2를 위한 ReplicaSet이 만들어진다.

업데이트가 진행되면 v2 ReplicaSet의 Pod 수는 늘어나고, v1 ReplicaSet의 Pod 수는 줄어든다. 문제가 생기면 이전 ReplicaSet으로 되돌리는 방식으로 롤백할 수 있다.

## 배포 전략이 중요한 이유

운영 환경에서는 배포 중에도 서비스가 계속 살아 있어야 한다. 롤링 업데이트는 일부 Pod만 교체하면서 서비스 중단을 줄인다.

다만 애플리케이션이 상태를 많이 가지거나, 버전 간 호환성이 낮다면 배포 전략을 더 신중하게 잡아야 한다.

## 정리

Deployment는 Pod 배포와 업데이트를 관리하는 오브젝트다. 내부적으로 ReplicaSet을 사용해 Pod 개수를 유지하고, 이미지 변경 시 롤링 업데이트를 수행한다. 쿠버네티스에서 애플리케이션을 운영하려면 Deployment를 중심으로 이해하는 것이 좋다.
