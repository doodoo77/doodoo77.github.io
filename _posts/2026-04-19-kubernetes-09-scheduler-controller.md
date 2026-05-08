---
title: "Scheduler와 Controller가 상태를 맞추는 방식"
date: 2026-04-19 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, scheduler, controller]
---

API Server와 etcd가 요청과 상태 저장을 담당한다면, Scheduler와 Controller는 실제로 클러스터가 움직이게 만드는 역할을 한다. 이 둘은 원하는 상태를 실제 상태로 바꾸는 과정에서 핵심적인 역할을 한다.

## Scheduler는 Pod의 위치를 정한다

Pod가 새로 만들어져야 할 때 처음부터 어느 Node에서 실행될지 정해져 있지는 않다. Scheduler는 아직 배치되지 않은 Pod를 보고, 어떤 Node가 적절한지 판단한다.

판단 기준에는 Node의 리소스 여유, 제약 조건, 정책 등이 포함된다. 예를 들어 CPU와 메모리가 부족한 Node에는 Pod를 배치하지 않는 식이다.

Scheduler의 역할은 실제 Pod를 실행하는 것이 아니라, Pod가 실행될 Node를 선택하는 것이다. 실행은 해당 Node의 kubelet이 담당한다.

## Controller는 원하는 상태를 계속 확인한다

Controller는 현재 상태와 원하는 상태를 비교한다. 차이가 있으면 그 차이를 줄이기 위한 작업을 만든다.

예를 들어 ReplicaSet의 원하는 Pod 수가 3인데 현재 Pod가 2개라면, Controller는 Pod를 하나 더 만들도록 한다. 반대로 현재 Pod가 4개라면 하나를 줄이는 방향으로 동작한다.

## Controller가 중요한 이유

쿠버네티스는 한 번 명령을 실행하고 끝나는 시스템이 아니다. 계속 감시하고 조정한다. 컨테이너가 죽거나 Node에 문제가 생겨도 원하는 상태를 유지하려고 한다.

이 반복적인 조정 과정을 Reconciliation이라고 볼 수 있다. 쿠버네티스의 안정성은 이 구조에서 나온다.

## Controller와 Scheduler의 연결

Controller가 새 Pod가 필요하다고 판단하면 Pod 생성 요청이 만들어진다. 그러면 Scheduler가 그 Pod를 어느 Node에 둘지 결정한다. 이후 선택된 Node의 kubelet이 실제 컨테이너를 실행한다.

즉, Controller는 필요한 작업을 만들고, Scheduler는 위치를 정하고, kubelet은 실행한다.

## 정리

Scheduler는 Pod의 배치 위치를 정하고, Controller는 원하는 상태와 현재 상태를 계속 맞춘다. 두 구성 요소가 함께 동작하면서 쿠버네티스는 컨테이너를 자동으로 복구하고 확장하고 조정한다.
