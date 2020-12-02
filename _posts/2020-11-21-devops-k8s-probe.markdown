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
	- [`What is Probe`](#what-is-probe)
	- [`Readiness Probe`](#readiness-probe)
	- [`Liveness Probe`](#liveness-probe)
  
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

- `ExecAction` : 컨테이너에서 지정된 명령어 실행
- `TCPSocketAction` : 지정된 포트에서 컨테이너 IP주소에 TCP 활성여부 검사
- `HTTPGetAction` : 지정한 포트 및 경로에서 컨테이너 IP주소에 HTTP GET 요청 수행

이제 Probe의 종류인 **`Readiness Probe, Liveness Probe, startupProbe`** 에 대해 어느상황에 어떤 설정으로 어떻게 사용이 되는지 알아보자.

<br>

---

#### **`Readiness Probe`**

우리가 최초 파드를 만들면 그안에 컨테이너가 생기고, 파드와 컨테이너의 상태가 러닝이 되면서 내부에 있는 앱도 정상적으로 구동이 될것이다. 그리고 서비스와 연결이 된후, 서비스의 IP가 외부에 알려지면서 외부에서는 이 서비스를 통해 많은 사람들이 실시간으로 접근을 하게된다. 

> 한 서비스에 두개의 파드가 연결되어있기때문에 50퍼센트씩 트래픽이 나뉘어진다는 가정에서의 설명이다.

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/probe/1.jpeg)

<br>

이때 node2 서버가 다운이 되면서 `node1의 pod1에 100퍼센트로 트래픽`이 몰리게 되고, pod1이 견뎌준다면 서비스는 문제없이 잘 될것이다. 이와 동시에 죽은 pod2는 autohealing 기능으로 다른 노드에 재생성되고, 그 과정에서 파드와 컨테이너가 Running상태가 되면서 서비스와 다시 연결이 된다. 

해당 경우 앱이 아직 완전히 구동에 성공하지 않은 상태라면, 서비스와 연결이 되자마자 **트래픽이 pod2로 유입**이 되기때문에 사용자는 **`50퍼센트의 확률로 에러페이지`** 를 보게된다.

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/probe/2.jpeg)

<br>

이 경우 **`ReadinessProbe`** 를 주게되면 이런 문제를 회피할 수 있다. ReadinessProbe는 앱이 정상적으로 구동되기전까지는 서비스와 연결이 되지않게 해준다. 그래서 pod2의 상태는 running이지만 트래픽은 계속 pod1에만 몰리게 구현할 수 있다. 앱이 완전히 준비하는것이 확인이되면, 서비스와 연결이되면서 다시 **`트래픽이 50대 50으로`** 흐르게된다.

<br>

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/probe/3.jpeg)

<br>

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/probe/4.jpeg)

<br>

---

#### **`Liveness Probe`**

이때 갑자기 해당 앱에 장애가 나고 **pod는 러닝**상태이고 서비스를 톰캣으로 돌리는경우 톰캣은 구동중이지만 그 위에 떠있는 앱의 메모리 오버플로우와 같은 문제가 생겨서 접속을 하는순간 500에러가 발생한다. 이는 톰캣자체의 프로세서가 죽은것이 아니라 그 위에 돌고있는 서비스에 문제가 생긴경우이다. 톰캣 프로세서를 보고있는 파드입장에서는 **계속 running상태**로 있게되고, 이 상황이 되면 해당 컨테이너에 흐르던 트래픽은 문제가 발생하게된다.

<br>

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/probe/5.jpeg)

<br>

이때 앱에대한 장애상황을 감지해주는게 `Liveness Probe`이다. 파드를 만들때 Liveness Probe를 달아주게되면 해당앱에 문제가 생긴 후 파드를 재실행하게 만들어서 잠깐의 트래픽에러는 발생하겠지만, 지속적으로 에러가 발생하여 장애상황이 생기는것을 방지해준다.

---
참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/#%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88-%ED%94%84%EB%A1%9C%EB%B8%8C-probe)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)