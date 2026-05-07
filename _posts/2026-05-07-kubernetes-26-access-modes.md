---
title: "RWO, RWOP, ROX, RWX 접근 모드 정리"
date: 2026-05-07 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, storage, access-modes]
---

PVC를 만들 때 accessModes를 설정한다. 이 값은 볼륨이 어떤 방식으로 어디에 마운트될 수 있는지 나타낸다. 대표적으로 RWO, RWOP, ROX, RWX가 있다.

## RWO

RWO는 ReadWriteOnce의 약자다. 하나의 Node에서 읽기와 쓰기가 가능하다는 의미다. 여러 Pod가 같은 Node에 있다면 접근 가능한 경우도 있지만, 기본적으로 하나의 Node에 붙는 볼륨으로 이해하는 것이 좋다.

데이터베이스처럼 하나의 인스턴스가 전용으로 쓰는 볼륨에서 자주 사용된다.

## RWOP

RWOP는 ReadWriteOncePod의 약자다. 하나의 Pod만 읽기와 쓰기를 할 수 있다는 의미다. RWO보다 더 강하게 단일 Pod 사용을 제한한다.

동시에 여러 Pod가 같은 볼륨에 쓰면 문제가 생길 수 있는 워크로드에 적합하다.

## ROX

ROX는 ReadOnlyMany의 약자다. 여러 Node나 Pod가 읽기 전용으로 접근할 수 있다. 공통 설정 파일이나 읽기 전용 데이터셋을 여러 Pod에서 공유해야 할 때 사용할 수 있다.

쓰기 작업은 허용되지 않기 때문에 데이터 변경이 필요한 경우에는 맞지 않는다.

## RWX

RWX는 ReadWriteMany의 약자다. 여러 Node나 Pod가 읽기와 쓰기를 함께 할 수 있다. 여러 애플리케이션 인스턴스가 같은 파일 시스템을 공유해야 할 때 유용하다.

다만 모든 스토리지가 RWX를 지원하는 것은 아니다. 보통 네트워크 파일 시스템 계열에서 지원하는 경우가 많다.

## 선택 기준

단일 데이터베이스 Pod가 전용 볼륨을 사용한다면 RWO나 RWOP가 적합하다. 여러 Pod가 읽기만 하면 ROX를 고려할 수 있다. 여러 Pod가 동시에 읽고 써야 한다면 RWX가 필요하다.

## 정리

Access Mode는 볼륨을 어떤 방식으로 공유할 수 있는지 정하는 조건이다. RWO는 하나의 Node, RWOP는 하나의 Pod, ROX는 여러 곳에서 읽기 전용, RWX는 여러 곳에서 읽기와 쓰기를 의미한다.
