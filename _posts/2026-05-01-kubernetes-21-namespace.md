---
title: "Namespace로 리소스 범위 나누기"
date: 2026-05-01 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, namespace, resource]
---

쿠버네티스 클러스터 하나에는 여러 애플리케이션과 여러 팀의 리소스가 함께 존재할 수 있다. 이때 모든 리소스를 한 공간에 두면 관리가 어려워진다. Namespace는 리소스의 논리적 범위를 나누는 데 사용한다.

## Namespace의 역할

Namespace는 하나의 클러스터 안에서 리소스를 구분하는 논리적 공간이다. 예를 들어 개발 환경은 `dev`, 운영 환경은 `prod`, 모니터링 도구는 `monitoring` Namespace에 둘 수 있다.

이렇게 나누면 같은 이름의 리소스라도 Namespace가 다르면 별도로 존재할 수 있다. `dev`의 `web` Deployment와 `prod`의 `web` Deployment는 서로 다른 리소스다.

## 서비스 단위로 묶는 기준

Namespace는 특정 서비스나 프로젝트를 묶는 기준으로도 사용할 수 있다. 한 서비스에 필요한 Deployment, Service, ConfigMap, Secret 등을 같은 Namespace에 두면 관리 범위가 명확해진다.

`kubectl` 명령에서도 Namespace를 지정하면 해당 범위의 리소스만 조회하거나 수정할 수 있다.

```bash
kubectl get pods -n dev
```

## 권한과 리소스 제한에도 연결된다

Namespace는 단순 분류만 하는 것이 아니다. RBAC 권한을 Namespace 단위로 줄 수 있고, ResourceQuota를 통해 Namespace별 리소스 사용량을 제한할 수도 있다.

팀 단위로 클러스터를 공유할 때 특히 중요하다. 모든 사용자가 모든 리소스를 건드릴 수 있으면 운영 위험이 커진다.

## Namespace가 나누지 않는 것

Namespace가 모든 것을 완전히 격리하는 것은 아니다. Node, PersistentVolume 같은 일부 리소스는 클러스터 범위 리소스다. 네트워크 격리도 Namespace만으로 자동 보장되는 것은 아니며 NetworkPolicy 같은 추가 설정이 필요하다.

## 정리

Namespace는 쿠버네티스 리소스를 논리적으로 나누는 단위다. 환경, 팀, 서비스 기준으로 리소스를 구분하고 권한과 리소스 제한을 적용하는 데 유용하다. 다만 완전한 보안 격리 수단으로 보려면 추가 설정이 필요하다.
