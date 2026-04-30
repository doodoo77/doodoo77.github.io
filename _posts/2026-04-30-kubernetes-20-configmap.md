---
title: "ConfigMap으로 설정을 애플리케이션에서 분리하기"
date: 2026-04-30 09:00:00 +0900
categories: [kubernetes]
tags: [kubernetes, configmap, configuration]
---

애플리케이션을 운영하다 보면 코드와 설정을 분리해야 한다. 환경마다 설정 값이 다를 수 있기 때문이다. 쿠버네티스에서는 ConfigMap을 사용해 설정 데이터를 별도로 관리할 수 있다.

## ConfigMap이 필요한 이유

개발 환경과 운영 환경은 데이터베이스 주소, 로그 레벨, 기능 플래그 같은 설정이 다를 수 있다. 이런 값을 애플리케이션 코드나 이미지 안에 고정해두면 환경이 바뀔 때마다 이미지를 다시 만들어야 한다.

ConfigMap을 사용하면 설정을 쿠버네티스 리소스로 분리할 수 있다. 애플리케이션 이미지는 그대로 두고, 설정만 바꿔서 다른 환경에 배포할 수 있다.

## 파일에서 ConfigMap 만들기

설정 파일이 `config-file.yml`에 있다면 다음처럼 ConfigMap을 만들 수 있다.

```bash
kubectl create cm my-config --from-file=config-file.yml
```

이 명령은 파일 내용을 `my-config`라는 ConfigMap 리소스로 저장한다. ConfigMap은 쿠버네티스 내부 저장소에 관리되므로, Pod가 어느 Node에서 실행되든 동일하게 접근할 수 있다.

## Pod에서 ConfigMap 사용하기

Pod는 ConfigMap을 환경 변수로 읽을 수도 있고, 파일처럼 마운트해서 사용할 수도 있다. 설정 파일 형태가 필요하다면 Volume처럼 마운트하는 방식이 자연스럽다.

예를 들어 alpine Pod에서 `my-config`를 파일로 읽게 구성할 수 있다.

## ConfigMap 변경 시 주의할 점

ConfigMap은 설정을 분리해주지만, 애플리케이션이 자동으로 새 설정을 다시 읽는 것은 아니다. 많은 애플리케이션은 시작 시점에 설정을 읽는다. 이 경우 ConfigMap을 수정한 뒤 Pod를 재시작해야 변경된 설정이 반영된다.

## 정리

ConfigMap은 애플리케이션 코드와 설정을 분리하는 리소스다. 환경별 설정을 이미지에 고정하지 않고 쿠버네티스에서 관리할 수 있다. 다만 설정 변경 후 Pod 재시작이 필요한 경우가 많다는 점을 기억해야 한다.
