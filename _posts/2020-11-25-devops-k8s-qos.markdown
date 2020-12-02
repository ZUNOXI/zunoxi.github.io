---
layout: post
title: Kubernetes 14. Kubernetes의 QoS
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `QoS classes`에 대한이해
  
- 목차
	- [`QoS 분류`](#qos-분류)
	- [`우선순위`](#우선순위-매커니즘)
  
## QoS on Kubernetes
---
쿠버네티스에는 기존 사용중인 파드에서 클러스터 자원의 `리소스에 대한 추가할당이 필요한 상황`이 발생할떄, 우선순위를 정해서 요청한 `자원들을 배치`하거나 `해당 파드를 삭제`하는 기능이 있다. 이를 QoS(Quality of Service)라고 부르며 이번 포스틍에서는 QoS에 대해 알아보려한다. 

<br><br><br>

**참고문서**
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/tasks/configure-pod-container/quality-service-pod/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)

<br>

---
### **`QoS 분류`**

쿠버네티스 서버에서 AP운영을 하게되다보면 **한정된 자원에대해 파드들이 요청**을하고, **우선순위**를 정해서 이를 처리해야 하는경우가 발생할 수 있을 것이다. 

관련해서 다음과 같은 예시를 설명한다.

> 예) 노드에 리소스가 있다고 가정하고, 노드위에 파드가 3개 만들어져서 균등하게 자원을 사용하고 있는 상태이다. 

여기서 갑자기 특정 파드에서 리소스를 더 필요로 하는 경우에는 `다른 파들를 죽이고 리소스를 더해야할지`, 아니면 파드는 리소스를 더 늘리지 못한 상태로 `죽어야할지`에 대한 선택이 필요하게 된다.

<br>

쿠버네티스는 이런 상황에 대비하여 각 파드당 앱의 중요도에 따라 관리할 수 있도록 세가지 단계로 QoS를 지원한다. 각단계는 중요한 순서대로 **`Guaranted, Burstable, BestEffort`** 로 분류된다.

<br>

---

#### **`우선순위 매커니즘`**

<br>

자, 먼저 pod1, pod2, pod3가 존재하며 각각 `Guaranted, Burstable, BestEffort` QoS 분류가 되어있다고 가정해보자. 이상태에서 pod1이 자원을 추가로 요청하게 되었고, 해당 pod1이 있는 node에는 여유자원이 없는 상태이다.

이때, 쿠버네티스는 **BestEffort로 QoS분류가 되어있는 pod3를 죽이고** 해당 pod에 있던 자원들중 `pod1이 필요한만큼의 자원을 할당`후 node의 여유자원으로 회수하게된다.

현재 pod1, pod2만 node에서 운영중인 상태에서 pod2가 현재 노드의 여유자원보다 많이 자원을 요청하게되는경우, pod2의 **Burstable은** pod1의 Guaranted QoS분류보다 우선순위가 낮기때문에 `추가적인 자원을 할당받지 못하고 죽게된다.`

<br>

아래 그림들을 참고하여 이해하면 좋을 것 같다.

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/qos/1.jpeg)

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/qos/2.jpeg)

<br>

> 여기서 주의할 것은 QoS는 특정속성에서 설정하는것이 아니라 컨테이너의 리소스설정에서 메모리와 CPU설정을 해주는 것이다.

이제 각 단계별 분류에 조금더 자세하게 확인해보겠다.

---

#### **`BestEffort`**

BestEffort는 pod의 어떤 container내에도 Request와 Limit가 미설정 되어있는 경우이다.

<br>

#### **`Burstable`**

Burstable은 설정은 되어있는데 어딘가 조금 부족한(?)상태로 설정되어있는 경우이다. 이는 다음과 같은 여러가지의 경우가 있다.

- 컨테이너의 Request와 Limit의 자원값이 일치하지 않는경우
- 컨테이너의 Request 혹은 Limit 값중 하나만 설정이 되어있는 경우
- 파드한개내의 다수의 컨테이너가 있을경우, Request와 Limit가 설정되지 않는 컨테이너가 있는경우

> 우선순위가 밀려 삭제되는경우에는 메모리가 Request에 비해 사용중인 App으로 **`OOM score`**(Out of Memory)를 계산하여 사용량이 높은 Pod가 먼저 삭제된다.

<br>

#### **`Guaranteed`**

모든 Container에 Request와 Limit가 설정되어있고, Request와 Limit에는 Memory와 CPU가 모두 설정되어있다. 각 Container내에 Memory와 CPU의 Request와 Limit값이 같아야한다.



