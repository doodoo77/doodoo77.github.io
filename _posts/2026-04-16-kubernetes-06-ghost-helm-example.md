---
title: "Ghost와 MariaDB 예제로 보는 애플리케이션 구성"
date: 2026-04-16 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, helm, ghost]
---

쿠버네티스 개념은 오브젝트를 따로 공부할 때보다 실제 애플리케이션 구성을 떠올릴 때 더 잘 이해된다. 예를 들어 블로그 엔진인 Ghost와 데이터베이스인 MariaDB를 클러스터에 올리는 상황을 생각해보자.

## 애플리케이션은 보통 하나의 컨테이너로 끝나지 않는다

Ghost는 웹 애플리케이션이다. 사용자가 브라우저로 접속하면 Ghost 컨테이너가 요청을 처리한다. 하지만 게시글과 설정 같은 데이터는 별도의 데이터베이스에 저장해야 한다. 그래서 MariaDB 컨테이너도 함께 필요하다.

이 경우 최소한 두 종류의 실행 단위가 필요하다.

- Ghost 애플리케이션 Pod
- MariaDB 데이터베이스 Pod

그리고 두 Pod가 서로 통신할 수 있어야 한다.

## Service가 내부 연결을 담당한다

Ghost가 MariaDB에 접근하려면 MariaDB Pod의 IP를 직접 알고 있어야 할 것 같지만, 실제로는 Service를 사용한다. MariaDB 앞에 Service를 만들고, Ghost는 그 Service 이름으로 데이터베이스에 접근한다.

이렇게 하면 MariaDB Pod가 재시작되어 IP가 바뀌어도 Ghost 설정을 매번 바꿀 필요가 없다.

## 외부 접속은 Ingress나 LoadBalancer가 담당한다

사용자가 Ghost 블로그에 접속하려면 외부 요청을 클러스터 내부로 들여와야 한다. 이때 Ingress를 사용하면 도메인 기반으로 Ghost Service에 요청을 보낼 수 있다.

클라우드 환경에서는 LoadBalancer 타입의 Service를 함께 쓰는 경우도 많다. 결국 목표는 외부 사용자가 안정적인 주소로 접속하고, 내부에서는 적절한 Pod까지 요청이 전달되게 하는 것이다.

## Helm은 묶음 설치를 도와준다

Ghost와 MariaDB를 각각 YAML로 작성해 설치할 수도 있지만, 실제 운영에서는 구성 파일이 많아진다. Helm은 여러 Kubernetes 리소스를 하나의 Chart로 묶어서 설치할 수 있게 해준다.

예를 들어 다음 명령처럼 Ghost Chart를 설치할 수 있다.

```bash
helm install ghost bitnami/ghost
```

이 명령 하나로 애플리케이션, 데이터베이스, Service, 설정, 저장소 관련 리소스가 함께 구성될 수 있다.

## 정리

실제 애플리케이션은 Pod 하나로 끝나지 않는다. 애플리케이션 Pod, 데이터베이스 Pod, 내부 연결을 위한 Service, 외부 노출을 위한 Ingress, 데이터 유지를 위한 Volume이 함께 필요하다. Helm은 이런 구성을 패키지처럼 설치하게 해준다.
