---
layout: post
title: Kubernetes 13. Kubernetes의 Probe
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 Container `Probe`에 대한이해
  
- 목차
	- [`DaemonSet`](#dameonset)
	- [`job`](#job--cronjob)
	- [`cronjob`](#cronjob)
  
## Container Probe
---
쿠버네티스 클러스터에서 프로브(probe)는 Pod의 상태를 체크하며 쿠버네티스 운영의 안정성을 더해주는 기능을한다. kubelet은 실행중인 컨테이너들에 대해서 `Readiness Probe, Liveness Probe, startupProbe`를 수행할 수 있으며, 이번 포스팅은 해당 프로브에 대해 정리하려한다.

<br>

### **`What is Probe`**

---

쿠버네티스 공식문서에서는 프로브(Probe)에 대해 다음과 같이 설명한다. 

> 프로브는 컨테이너에서 kubelet에 의해 주기적으로 수행되는 진단(diagnostic)이다. 

즉, 상태의 진단을 위해 사용되는 특정 장치라고 할 수 있다. 진단결과는 `Success(진단통과), Failure(진단실패), Unknown(진단실패,동작수행X)` 3가지 중 하나를 가진다.

여기서 kubelet은 컨테이너에 설정되어있는 핸들러를 호출하고 그 타입은 총 3가지로 분류된다.

- ExecAction : 컨테이너에서 지정된 명령어 실행
- TCPSocketAction : 지정된 포트에서 컨테이너 IP주소에 TCP 활성여부 검사
- HTTPGetAction : 지정한 포트 및 경로에서 컨테이너 IP주소에 HTTP GET 요청 수행

이제 이 Probe의 종류인 `Readiness Probe, Liveness Probe, startupProbe`에 대해 어느상황에 어떤 설정으로 어떻게 사용이 되는지 알아보자.

<br>

---

#### **`Readiness Probe`**


---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%ED%94%84%EB%A1%9C%EB%B8%8C-probe)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)