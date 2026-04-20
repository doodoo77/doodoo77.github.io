---
title: "Worker Node의 kubelet과 kube-proxy"
date: 2026-04-20 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, kubelet, kube-proxy]
---

Control Plane이 클러스터의 상태를 결정한다면, Worker Node는 실제 애플리케이션을 실행한다. Worker Node에서 중요한 구성 요소가 kubelet과 kube-proxy다.

## kubelet은 Pod 실행을 담당한다

kubelet은 모든 Node에서 실행되는 에이전트다. API Server를 통해 자신이 실행해야 할 Pod 정보를 받고, 컨테이너 런타임을 통해 실제 컨테이너를 실행한다.

예를 들어 Scheduler가 어떤 Pod를 Node A에 배치하기로 결정하면, Node A의 kubelet이 그 정보를 확인하고 Pod를 만든다.

kubelet은 단순히 실행만 하는 것이 아니라 상태도 확인한다. Pod가 정상적으로 실행 중인지, 컨테이너가 죽지는 않았는지 같은 정보를 계속 확인하고 API Server에 보고한다.

## kube-proxy는 Service 네트워크를 구성한다

쿠버네티스에서 Service는 고정된 진입점을 제공하고, 뒤에 있는 Pod로 트래픽을 전달한다. kube-proxy는 이 Service 트래픽이 실제 Pod로 가도록 네트워크 규칙을 구성한다.

구현 방식으로는 iptables나 IPVS가 사용될 수 있다. 사용자는 Service IP로 접근하지만, 실제 요청은 kube-proxy가 구성한 규칙에 따라 알맞은 Pod로 전달된다.

## Pod 생성 흐름 다시 보기

Pod가 하나 만들어지는 과정을 Worker Node 관점에서 보면 다음과 같다.

1. 사용자가 Pod 생성 요청을 보낸다.
2. API Server가 요청을 받고 etcd에 저장한다.
3. Controller가 필요한 상태 변화를 감지한다.
4. Scheduler가 Pod를 배치할 Node를 선택한다.
5. 선택된 Node의 kubelet이 Pod를 실행한다.
6. kubelet이 실행 결과를 API Server에 보고한다.

## 네트워크 흐름도 함께 이해하기

Pod는 자체 IP를 가지지만, Pod IP는 고정된 대상으로 보기 어렵다. 그래서 Service를 사용한다. kube-proxy는 Service가 Pod로 연결되도록 각 Node에 네트워크 규칙을 만들어둔다.

이 덕분에 사용자는 Service를 통해 안정적으로 접근할 수 있고, Pod가 바뀌어도 연결 구조가 유지된다.

## 정리

kubelet은 Worker Node에서 Pod를 실제로 실행하고 상태를 보고한다. kube-proxy는 Service 트래픽이 Pod로 전달되도록 네트워크 규칙을 구성한다. 둘은 Worker Node가 쿠버네티스 클러스터의 실행 단위로 동작하게 만드는 핵심 요소다.
