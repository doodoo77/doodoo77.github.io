---
title: "NodePort와 LoadBalancer 흐름 이해하기"
date: 2026-04-26 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, nodeport, loadbalancer]
---

Service는 내부 통신뿐 아니라 외부에서 클러스터 내부 애플리케이션으로 접근하는 데도 사용된다. 이때 자주 만나는 타입이 NodePort와 LoadBalancer다.

## NodePort

NodePort는 각 Node의 특정 포트를 열고, 그 포트로 들어온 요청을 Service로 전달한다. 예를 들어 NodePort가 31000이라면 외부 사용자는 `NodeIP:31000`으로 접근할 수 있다.

요청 흐름은 다음처럼 볼 수 있다.

```text
외부 사용자 → NodeIP:NodePort → Service → Pod
```

NodePort는 구조가 단순하지만, 서비스가 많아질수록 포트 관리가 번거로워진다. 서비스마다 포트를 열어야 하므로 운영 환경에서는 단독으로 쓰기보다 다른 구성과 함께 쓰는 경우가 많다.

## LoadBalancer

LoadBalancer 타입은 클라우드 환경에서 외부 로드밸런서를 만들어 Service와 연결하는 방식이다. AWS, GCP, Azure 같은 클라우드에서는 LoadBalancer 타입 Service를 만들면 외부 IP나 DNS가 할당될 수 있다.

요청 흐름은 다음과 비슷하다.

```text
외부 사용자 → Cloud LoadBalancer → NodePort → Service → Pod
```

클러스터 내부에는 여러 Node와 여러 Pod가 있을 수 있지만, 사용자는 하나의 외부 주소로 접근한다.

## NodePort와 LoadBalancer의 차이

NodePort는 Node의 포트를 직접 열어 접근하는 방식이다. LoadBalancer는 클라우드 로드밸런서가 앞단에서 요청을 받아 적절한 Node와 Service로 전달하는 방식이다.

운영 환경에서는 LoadBalancer가 더 자연스러운 경우가 많다. 하지만 서비스가 많을 때마다 LoadBalancer를 하나씩 만드는 것도 비용과 관리 문제가 생길 수 있다.

## Ingress로 넘어가는 이유

여러 웹 서비스를 운영한다면 서비스마다 LoadBalancer를 따로 만드는 것보다 Ingress를 사용하는 것이 낫다. Ingress는 하나의 진입점에서 도메인이나 경로 기준으로 여러 Service에 요청을 나눠 보낼 수 있다.

## 정리

NodePort는 Node의 포트를 열어 외부 요청을 받는 방식이고, LoadBalancer는 클라우드 로드밸런서를 통해 Service를 외부에 노출하는 방식이다. 서비스가 많아지면 Ingress를 함께 고려해야 한다.
