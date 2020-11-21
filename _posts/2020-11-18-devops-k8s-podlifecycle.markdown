---
layout: post
title: Kubernetes 12. Kubernetes의 Pod의 Lifecylce
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스 `Pod의 라이프사이클`에 대한이해
  
- 목차
	- [`DaemonSet`](#dameonset)
	- [`job`](#job--cronjob)
	- [`cronjob`](#cronjob)
  
## Lifecycle of Pod 
---
쿠버네티스 클러스터 오브젝트의 가장 작은 단위인 Pod(이하 파드)는 파드의 수명 중 한번만 스케쥴된다. 파드가 노드에 스케줄되면, 파드는 중지되거나 종료될 때까지 해당 노드에서 실행된다. 이번 포스팅에서는 파드의 라이프 사이클에 대해 정리한다.

<br>

### **`Pod lifecycle`**

---

파드의 status 필드는 phase 필드를 포함하는 PodStatus 오브젝트로 정의된다. 파드(Pod)는 아래 그림처럼 파드만의 라이프사이클이 있다.


파드는 `Pending` 단계에서 시작해서, 기본 컨테너 중 적어도 하나 이상이 OK로 시작하면 `Running` 단계를 통과하고, 그런 다음 파드의 컨테이너가 실패로 종료되었는지 여부에 따라 `Succeeded` 또는 `Faild` 단계로 이동한다.




---

#### **`Pending`**

파드의 최초상태는 펜딩(pending)이다.

펜딩단계에서는 다음과 같은 몇가지 옵션을 설정할 수 있다.

<br>

✓ initContainer

본 컨테이너가 가동되기전에 볼륨이나 보안세팅을 위해 `사전세팅을하며 초기화`시켜야 하는경우가 있다. 해당 경우에 initContainer를 파드 생성의 스크립트에 추가하면, 해당 설정이 본 컨테이너보다 먼저 실행이되고 `Initialized`에 사전작업이 **성공적으로 끝날경우 True, 실패를 했을 경우 False를 반환**한다.

<br> 

✓ Node Scheduling

 파드가 어느 노드위에 올라갈지 직접 노드를 지정했을때는 **지정한 해당노드에 생성**되게 할 수 있다. 혹은 쿠버네티스가 알아서 자원의 상황을 파단하고 파드가 생성될 노드를 결정하여 생성할 수 있게 한다. 해당 작업이 완료되면 `PodScheduled`는 True가 된다.


> 위 두옵션들이 실행될동안 컨테이너의 상태는 **`waiting`**, Reason은 **`ContainerCreating`** 상태이다.

---

#### **`Running`**

Pod가 Pending이후 실행될 때, 하나또는 모든 컨테이너의 기동중에 문제가 발생하면 컨테이너의 상태는 `Waiting`이되고, `CrashLoopBackOff`라는 Reason상태값이 반환되게된다.

이때 파드는 컨테이너의 이러한 상태들에 대해서 Pending이 아닌 Running으로 간주하고 대신 내부 컨디션에 ContainerReady와 Ready는 False의 값을 갖게된다.

> 내부 컨디션이 정상적일때는 당연히 ContainerReady와 Ready가 True값을 가진다.

따라서 일반적으로 `서비스의 중단이 절대로 일어나면 안되는 시스템`인경우, 파드의 상태가 Running이라도 내부 컨테이너의 상태가 정상이 아닐 수 있기때문에 **`컨테이너의 상태도 모니터링을 해줘야한다.`**

<br>

`Job`이나 `CronJob`으로 생성된 파드의 경우, 자신의 일을 수행했을때는 Running중이지만, 일을 마치게되면 파드는 더이상 일을 하지 않는 상태가 되는데 이때 파드의 상태는 `Faild` 혹은 `Succeeded`상태로 나뉘게 된다.

작업을 하는도중 컨테이너 중 하나라도 문제가 생겨서 에러가 발생하면 파드의 상태는 Faild가 되고, 모든 컨테이너들이 완료되면 Succeeded가 되는 차이이다. 이는 파드의 컨디션값이 변경된게된다.

<br>

`Pending중`에 바로 Faild로 빠지는 경우도 있고, Pending이나 Running중에 통신장애가 발생하면 파드가 **Unknown**상태로 변경되는데, 장애가 빨리 해결되면 다시 기존의 상태로 변경이 되지만 계속 장애가 지속되면 Faild상태로 바뀌기도 한다.

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/pods/pod-lifecycle/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)