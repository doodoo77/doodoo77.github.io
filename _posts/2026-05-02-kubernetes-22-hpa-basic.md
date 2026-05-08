---
title: "HPA는 부하를 보고 Pod 수를 조정한다"
date: 2026-05-02 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, hpa, autoscaling]
---

운영 환경에서는 트래픽이 항상 일정하지 않다. 사용자가 몰리는 시간에는 Pod가 더 필요하고, 한가한 시간에는 Pod를 줄여도 된다. 쿠버네티스의 HPA는 이런 수평 확장을 자동화한다.

## HPA란 무엇인가

HPA는 Horizontal Pod Autoscaler의 약자다. Deployment 같은 리소스의 replicas 값을 자동으로 조정한다. 예를 들어 CPU 사용률이 높으면 Pod 수를 늘리고, 낮으면 줄인다.

HPA가 직접 Pod를 만드는 것은 아니다. HPA는 Deployment의 replicas 값을 바꾸고, Deployment와 ReplicaSet이 그 값에 맞춰 Pod 수를 조정한다.

## 동작 흐름

흐름은 다음처럼 볼 수 있다.

```text
CPU 사용률 증가 → HPA가 replicas 증가 판단 → Deployment replicas 변경 → ReplicaSet이 Pod 추가 생성
```

이 구조를 알면 HPA가 쿠버네티스의 다른 리소스와 어떻게 연결되는지 이해하기 쉽다.

## 기본 명령 예시

CPU 사용률 50퍼센트를 기준으로 최소 1개, 최대 10개까지 Pod를 조정하려면 다음처럼 설정할 수 있다.

```bash
kubectl autoscale deployment php-apache --cpu=50% --min=1 --max=10
```

특정 Namespace에 있는 Deployment라면 다음처럼 Namespace를 지정한다.

```bash
kubectl autoscale deployment apache-server -n autoscale --cpu=50% --min=1 --max=4
```

## minReplicas와 maxReplicas

HPA에는 최소 Pod 수와 최대 Pod 수가 있다. 최소값은 트래픽이 적어도 유지할 기본 인스턴스 수를 의미하고, 최대값은 무한정 늘어나는 것을 막는 상한선이다.

예를 들어 minReplicas가 1이고 maxReplicas가 4라면, HPA는 Pod 수를 1개에서 4개 사이로만 조정한다.

## 정리

HPA는 부하를 보고 Deployment의 replicas 값을 조정하는 자동 확장 리소스다. Pod를 직접 만들기보다 Deployment를 조정하고, Deployment와 ReplicaSet이 실제 Pod 수를 맞춘다.
