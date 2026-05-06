---
title: "PV, PVC, StorageClass의 역할 나누기"
date: 2026-05-06 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, pv, pvc, storageclass]
---

쿠버네티스에서 영구 저장소를 다룰 때 PV, PVC, StorageClass라는 용어를 자주 만난다. 이름이 비슷해서 헷갈리지만, 역할을 나눠 보면 구조가 단순해진다.

## PV는 실제 저장소다

PV는 PersistentVolume의 약자다. 클러스터에서 사용할 수 있는 실제 저장 공간을 의미한다. 관리자가 미리 만들어둘 수도 있고, StorageClass를 통해 동적으로 생성될 수도 있다.

PV는 특정 Pod에 종속되지 않는다. 클러스터 리소스로 존재하며, PVC 요청과 연결되어 사용된다.

## PVC는 저장소 요청서다

PVC는 PersistentVolumeClaim의 약자다. Pod가 필요한 저장소의 조건을 요청하는 리소스다. 예를 들어 10Gi 용량이 필요하고, 읽기와 쓰기가 가능한 볼륨이 필요하다고 선언할 수 있다.

Pod는 PV를 직접 고르기보다 PVC를 참조한다. 쿠버네티스는 PVC 조건에 맞는 PV를 찾아 연결한다.

## StorageClass는 생성 정책이다

StorageClass는 PVC 요청이 들어왔을 때 어떤 방식으로 PV를 만들지 정의하는 정책이다. 클라우드 환경에서는 StorageClass를 통해 디스크를 동적으로 생성하는 경우가 많다.

예를 들어 빠른 SSD 기반 StorageClass와 저렴한 HDD 기반 StorageClass를 나눠둘 수 있다. PVC에서 어떤 StorageClass를 사용할지 지정하면 된다.

## 세 가지의 관계

흐름은 다음처럼 볼 수 있다.

```text
Pod → PVC 요청 → StorageClass 정책 확인 → PV 생성 또는 매칭 → Pod에 마운트
```

Pod는 PVC를 사용하고, PVC는 조건에 맞는 PV와 연결된다. StorageClass는 그 PV를 어떻게 준비할지 정한다.

## 정리

PV는 실제 저장소, PVC는 저장소 요청서, StorageClass는 저장소 생성 정책이다. Pod가 데이터를 오래 유지해야 한다면 이 세 가지 관계를 이해해야 한다.
