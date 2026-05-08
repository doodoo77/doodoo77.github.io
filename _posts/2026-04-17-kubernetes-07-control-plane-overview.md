---
title: "Control Plane은 클러스터의 상태를 맞추는 중심이다"
date: 2026-04-17 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, control-plane, architecture]
---

쿠버네티스 클러스터는 크게 Control Plane과 Worker Node로 나눌 수 있다. Worker Node는 실제 애플리케이션이 실행되는 곳이고, Control Plane은 클러스터가 원하는 상태를 유지하도록 조정하는 중심이다.

![쿠버네티스 구조](/k8s.png)

## Control Plane의 역할

Control Plane은 사용자가 선언한 원하는 상태를 저장하고, 실제 클러스터 상태가 그에 맞게 유지되도록 만든다. 예를 들어 사용자가 `Pod 4개를 유지해줘`라고 선언하면, Control Plane은 현재 Pod 수를 확인하고 부족하면 만들고 많으면 줄이는 방향으로 동작한다.

이 구조를 이해하면 쿠버네티스가 단순 실행 도구가 아니라 상태 관리 시스템이라는 점이 보인다.

## 주요 구성 요소

Control Plane에는 여러 구성 요소가 있다.

- API Server
- etcd
- Scheduler
- Controller Manager

API Server는 쿠버네티스의 모든 요청이 들어오는 입구다. 사용자가 kubectl로 명령을 보내면 API Server가 요청을 받는다.

etcd는 클러스터 상태를 저장하는 key-value 저장소다. 원하는 상태와 현재 상태에 대한 정보가 저장된다.

Scheduler는 새로 생성해야 하는 Pod를 어느 Node에 배치할지 결정한다.

Controller는 현재 상태를 감시하면서 원하는 상태와 다르면 실제 리소스를 조정한다.

## Worker Node와의 관계

Control Plane이 결정을 내리면, Worker Node에서는 kubelet이 실제 Pod를 실행한다. 즉, Control Plane은 상태를 판단하고 조정하고, Worker Node는 애플리케이션을 실행한다.

이 둘이 함께 동작해야 쿠버네티스 클러스터가 정상적으로 운영된다.

## 정리

Control Plane은 쿠버네티스의 두뇌에 가깝다. 사용자의 요청을 받고, 상태를 저장하고, 배치를 결정하고, 원하는 상태를 유지하도록 조정한다. Worker Node는 그 결과를 실제 컨테이너 실행으로 반영한다.
