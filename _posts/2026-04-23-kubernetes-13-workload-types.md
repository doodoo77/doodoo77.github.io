---
title: "DaemonSet, StatefulSet, Job은 언제 쓸까"
date: 2026-04-23 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, workload, daemonset]
---

Deployment는 가장 많이 쓰는 Workload지만 모든 상황을 해결하지는 않는다. 쿠버네티스에는 애플리케이션 성격에 따라 여러 Workload 리소스가 있다. 대표적으로 DaemonSet, StatefulSet, Job이 있다.

## DaemonSet

DaemonSet은 모든 Node에 Pod를 하나씩 실행하고 싶을 때 사용한다. 예를 들어 로그 수집기, 모니터링 에이전트, 네트워크 플러그인처럼 각 Node마다 반드시 하나씩 있어야 하는 구성 요소에 적합하다.

새 Node가 클러스터에 추가되면 DaemonSet은 그 Node에도 자동으로 Pod를 만든다. 반대로 Node가 제거되면 해당 Pod도 함께 사라진다.

## StatefulSet

StatefulSet은 상태를 가진 애플리케이션을 운영할 때 사용한다. 대표적으로 데이터베이스처럼 Pod마다 고유한 이름과 저장소가 필요한 경우다.

Deployment는 Pod가 교체될 때 이름과 순서가 중요하지 않은 경우에 적합하다. 하지만 StatefulSet은 Pod를 순서대로 만들고, 안정적인 식별자를 제공한다. 볼륨을 재사용해야 하는 경우에도 StatefulSet이 유용하다.

## Job

Job은 특정 작업을 완료할 때까지 Pod를 실행하는 리소스다. 웹 서버처럼 계속 떠 있어야 하는 애플리케이션과 다르게, 배치 작업이나 일회성 작업에 적합하다.

예를 들어 데이터 마이그레이션, 파일 변환, 정기적 계산 작업 같은 경우 Job을 사용할 수 있다. 작업이 완료되면 Pod는 종료된다.

## 어떤 기준으로 고를까

계속 떠 있어야 하고 상태가 중요하지 않은 웹 애플리케이션이라면 Deployment가 기본 선택이다. 모든 Node에 하나씩 떠야 한다면 DaemonSet을 사용한다. 고유한 저장소와 순서가 필요하면 StatefulSet을 선택한다. 끝나야 하는 작업이라면 Job을 쓴다.

## 정리

쿠버네티스 Workload는 애플리케이션의 실행 방식을 표현한다. Deployment는 일반적인 서비스, DaemonSet은 노드별 에이전트, StatefulSet은 상태가 있는 서비스, Job은 완료되는 작업에 적합하다.
