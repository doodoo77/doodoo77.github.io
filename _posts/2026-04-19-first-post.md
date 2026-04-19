---
title: "쿠버네티스 정리"
date: 2026-04-19
---

![쿠버네티스 구조](이미지URL)

- Master Node: 쿠버네티스를 관리/ Worker Node: 실제 애플리케이션이 실행되는 곳
- kubectl(control): 사용자가 쿠버네티스에 명령을 내리는 도구, kubeadm(admin): 쿠버네티스 클러스터 설치와 초기 구성을 진행, kubelet: 각 노드에서 쿠버네티스의 지시를 받아 실행을 관리하는 에이전트
- etcd에는 리소스 상태 정보가 저장되고, Worker Node의 kubelet은 그 상태를 실제로 맞추도록 동작함 
- kube-proxy는 iptable/IPVS같은 방식으로 Service와 Pod간 전달 규칙을 미리 구성해두고, 실제 요청이 들어오면 그 규칙에 따라 네트워크가 적절한 Pod로 트래픽을 전달함  
