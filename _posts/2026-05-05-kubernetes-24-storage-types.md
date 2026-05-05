---
title: "Block, File, Object Storage 차이"
date: 2026-05-05 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, storage, volume]
---

쿠버네티스에서 저장소를 이해하려면 먼저 스토리지 유형을 구분해야 한다. 대표적으로 Block Storage, File Storage, Object Storage가 있다. 각각 접근 방식과 적합한 사용처가 다르다.

## Block Storage

Block Storage는 데이터를 블록 단위로 저장하는 디스크형 스토리지다. SSD나 HDD를 떠올리면 이해하기 쉽다. 읽기와 쓰기 성능이 중요할 때 자주 사용한다.

데이터베이스처럼 빠른 I/O가 필요한 워크로드에 적합하다. 보통 하나의 Pod가 자기 전용 볼륨을 사용하는 구조가 많고, StatefulSet과 함께 쓰는 경우도 많다.

## File Storage

File Storage는 파일과 폴더 단위로 접근하는 스토리지다. 네트워크 파일 시스템처럼 여러 Pod나 여러 Node가 같은 파일 시스템에 접근해야 할 때 유용하다.

예를 들어 여러 애플리케이션 인스턴스가 같은 업로드 디렉토리를 공유해야 한다면 File Storage를 고려할 수 있다.

## Object Storage

Object Storage는 데이터를 객체 단위로 저장하고 API로 접근한다. Amazon S3 같은 서비스가 대표적이다. 대용량 파일 저장과 확장성에 강하다.

Object Storage는 일반적으로 쿠버네티스 Volume으로 마운트해서 쓰기보다, 애플리케이션이 API를 통해 직접 요청하는 방식이 많다.

## 어떤 저장소를 선택할까

데이터베이스처럼 빠른 디스크 I/O가 필요하면 Block Storage가 적합하다. 여러 Pod가 파일 시스템을 공유해야 한다면 File Storage가 필요할 수 있다. 이미지, 백업 파일, 대용량 비정형 데이터는 Object Storage가 자연스럽다.

## 정리

Block Storage는 디스크처럼 빠른 읽기와 쓰기에 강하고, File Storage는 여러 클라이언트가 파일 단위로 공유하기 좋다. Object Storage는 API 기반의 대용량 저장에 적합하다. 쿠버네티스에서 볼륨을 설계할 때 이 차이를 먼저 잡아야 한다.
