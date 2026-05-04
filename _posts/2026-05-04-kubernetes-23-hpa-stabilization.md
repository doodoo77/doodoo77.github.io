---
title: "HPA의 scaleDown 안정화 옵션"
date: 2026-05-04 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, hpa, stabilization]
---

HPA를 설정할 때 단순히 CPU 기준과 최소, 최대 Pod 수만 보면 부족하다. 부하가 순간적으로 변할 때 Pod 수를 너무 자주 바꾸면 오히려 시스템이 불안정해질 수 있다. 그래서 scaleDown 안정화 옵션이 중요하다.

## 왜 안정화 시간이 필요할까

CPU 사용률은 순간적으로 오르내릴 수 있다. 요청이 잠깐 줄었다고 바로 Pod를 줄이면, 곧 다시 트래픽이 늘었을 때 대응이 늦어질 수 있다.

반대로 Pod를 늘리는 것은 장애를 막기 위해 빠르게 반응해야 할 때가 많다. 하지만 줄이는 작업은 조금 더 신중하게 해도 된다.

## stabilizationWindowSeconds

HPA에는 scaleDown 동작을 늦추는 안정화 창을 설정할 수 있다.

```yaml
behavior:
  scaleDown:
    stabilizationWindowSeconds: 30
```

이 설정은 scaleDown 판단이 나와도 일정 시간 동안 지켜보게 한다. 예를 들어 30초로 설정하면, Pod 수를 줄이기 전에 최근 상태를 조금 더 확인한다.

## kubectl edit로 수정하기

기존 HPA를 수정하려면 다음처럼 edit 명령을 사용할 수 있다.

```bash
kubectl edit hpa apache-server -n autoscale
```

열린 YAML에서 `behavior.scaleDown.stabilizationWindowSeconds` 값을 추가하거나 수정하면 된다.

## 너무 빠른 scaleDown의 위험

Pod 수가 자주 바뀌면 애플리케이션이 안정적으로 동작하기 어렵다. 특히 컨테이너 시작 시간이 길거나, 캐시 워밍업이 필요한 서비스에서는 불필요한 scaleDown이 성능 저하로 이어질 수 있다.

그래서 운영에서는 scaleUp은 빠르게, scaleDown은 천천히 가져가는 전략을 많이 쓴다.

## 정리

HPA는 자동 확장을 편하게 해주지만, Pod 수를 너무 자주 바꾸면 시스템이 흔들릴 수 있다. `stabilizationWindowSeconds`를 사용하면 scaleDown을 더 안정적으로 제어할 수 있다.
