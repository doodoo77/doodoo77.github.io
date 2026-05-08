---
title: "hostPath와 노드 로그 접근 방식"
date: 2026-04-29 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, volume, hostpath]
---

쿠버네티스 Volume에는 여러 종류가 있다. emptyDir이 Pod 내부의 임시 공유 공간이라면, hostPath는 Node의 파일 시스템 일부를 Pod에 연결하는 방식이다.

## hostPath란 무엇인가

hostPath는 Node의 특정 경로를 Pod 안으로 마운트한다. 예를 들어 Node의 `/var/log` 디렉토리를 컨테이너 안의 `/host/var/log`로 연결할 수 있다.

이렇게 하면 컨테이너가 Node의 로그 파일을 읽을 수 있다. 로그 수집기처럼 Node 단위의 파일에 접근해야 하는 경우 hostPath가 사용될 수 있다.

## 사용 예시

로그 수집 에이전트가 모든 Node의 시스템 로그를 읽어야 한다고 하자. 이때 DaemonSet으로 각 Node에 에이전트 Pod를 하나씩 띄우고, hostPath로 Node 로그 디렉토리를 연결할 수 있다.

구조는 다음과 같다.

```text
Node /var/log → Pod /host/var/log
```

에이전트는 컨테이너 내부 경로를 읽지만, 실제로는 Node의 파일을 읽는 셈이다.

## 주의할 점

hostPath는 강력하지만 위험할 수 있다. 컨테이너가 Node 파일 시스템에 접근하기 때문에 권한을 잘못 주면 보안 문제가 생길 수 있다.

또한 특정 Node의 경로에 의존하기 때문에 이식성이 떨어진다. 다른 Node에 같은 경로와 같은 파일 구조가 없다면 문제가 생길 수 있다.

## emptyDir과 비교

emptyDir은 Pod 내부에서 생성되는 임시 Volume이고, hostPath는 Node의 실제 경로를 연결한다. emptyDir은 Pod가 삭제되면 사라지고, hostPath는 Node 파일 시스템에 남아 있을 수 있다.

하지만 hostPath는 Node 의존성이 강하므로 운영 환경에서는 사용 목적을 분명히 해야 한다.

## 정리

hostPath는 Node의 파일 시스템을 Pod에 연결하는 Volume이다. 로그 수집처럼 Node 파일에 접근해야 하는 경우 사용할 수 있지만, 보안과 이식성 측면에서 주의가 필요하다.
