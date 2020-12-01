---
layout: post
title: Kubernetes 15. Kubernetes의 Node Scheduling
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `Node Scheduling`에 대한이해
  
- 목차
	- [`QoS 분류`](#qos-분류)
	- [`우선순위`](#우선순위-매커니즘)
  
## Node Scheduling on Kubernetes
---
쿠버네티스에서 파드를 생성할경우, 생성할 파드가 어떤 노드에 할당되어야할지 유저가 기본적인 설정을 해놓는다면 **`노드스케쥴러(Node Scheduler)`** 가 이를 실행한다. 이를 스케쥴링(Scheduling)이라고 부르며 이번 포스팅에서는 해당용어에 대해 알아보려한다.

<br>

### **`Scheduling`**

---

Pod는 기본적으로 스케쥴러에의해 할당되지만, 앞서 설명한것 처럼 사용자의 지정설정에 따라 커스터마이징해서 사용할 수가 있다.

그 설정방법은 다음과 같이 분류된다.

- `Node Name` : 노드 이름을 명시해서 배치할 노드에 pod를 배치하는것(실제 사용환경에서는 node가 삭제되면서 이름이 바뀔 수 있기때문에 잘 사용하지는 않는다.)

- `Node Scheduler` : Label을 달아놔서 해당 Label이 달린 노드에가서 pod가 배치된다. label이 중복되는 node들이 있는경우 자원이 가장 여유로운 노드에 할당이된다.

- `Node Affinity` : 파드에 Key만 설정해도 해당 키에 해당하는 노드중에 가장 자원이 여유로운곳에 배치되게되고, 만약 해당 조건에 맞지않는 키를 갖고 있더라도 스케쥴러가 판단하여 자원이 남는 노드에 배치하도록 옵션을 줄 수 있다.


✓ Node Affinity은 matchExpressions, required, preferred weight의 추가 옵션을 갖고있다.

<br>

---

#### **`Pod간 집중/분산`**

<br>

pod들을 노드에 배치할때 노드 스케쥴러를 사용하면 파드들간의 동시배치, 배타적 배치 등의 옵션을 사용할 수 있다.

- `Pod-Affinity` : master pod와 해당 파드와 함께 사용해야하는 slave pod가 있는경우, 같은 PV 호스트패스를 사용한다면 master pod가 최초 할당된 노드에 slave도 함께 생성된다.
- `Anti-Affinity` : Master와 Slave 구조일때 서로 다른 노드에 생성되게 해준다. 이는 같은 노드에 두개 pod가 같이 있으면 해당 node다운 시 서비스가 다운될 수 있는경우에 사용한다.

---

### **`Node 할당제한`**

노드 스케쥴러는 Toleration, Taint 기능을 사용하여 일반적인 경우에는 pod삽입이 되지않게, 조금은 특별하게 node 할당을 컨트롤할 수 있다. 이는 pod가 해당 노드를 직접 지정해도 할당이 되지않으며, toleration옵션이 달려있어야 할당이 된다.

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/kube-scheduler/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)