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
	- [`Scheduling`](#scheduling)
	- [`Pod간 집중/분산`](#pod간-집중분산)
	- [`Node 할당제한`](#node-할당제한)
  
## Node Scheduling on Kubernetes
---
쿠버네티스에서 파드를 생성할경우, 생성할 파드가 어떤 노드에 할당되어야할지 유저가 기본적인 설정을 해놓는다면 **`노드스케쥴러(Node Scheduler)`** 가 이를 실행한다. 이를 스케쥴링(Scheduling)이라고 부르며 이번 포스팅에서는 해당용어에 대해 알아보려한다.

<br><br><br>


**참고문서**
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/scheduling-eviction/kube-scheduler/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)

<br>

---
### **`Scheduling`**

Pod는 기본적으로 스케쥴러에의해 할당되지만, 앞서 설명한것 처럼 사용자의 지정설정에 따라 커스터마이징해서 사용할 수가 있다.

그 설정방법은 다음과 같이 분류된다.

- `Node Name` : 노드 이름을 명시해서 배치할 노드에 pod를 배치하는것(실제 사용환경에서는 node가 삭제되면서 이름이 바뀔 수 있기때문에 잘 사용하지는 않는다.)

- `Node Scheduler` : Label을 달아놔서 해당 Label이 달린 노드에가서 pod가 배치된다. label이 중복되는 node들이 있는경우 자원이 가장 여유로운 노드에 할당이된다.

- `Node Affinity` : 파드에 Key만 설정해도 해당 키에 해당하는 노드중에 가장 자원이 여유로운곳에 배치되게되고, 만약 해당 조건에 맞지않는 키를 갖고 있더라도 스케쥴러가 판단하여 자원이 남는 노드에 배치하도록 옵션을 줄 수 있다.

<br>

✓ **Node Affinity**은 `matchExpressions, required, preferred weight`의 추가 옵션을 갖고있다.

- `matchExpressions` : 여러조합으로 파드들을 선택할 수 있다. 레플리케이션 컨트롤러의 
  그것과 같이 pod에 해당 옵션을 설정하고 node들을 골라서 배치할 수 있다.

- `required vs preferred` : required는 파드에 설정한 키값이 반드시 해당 노드의 키값과 일치해야하는 경우 배치되고, preferred는 해당 키값을 선호하되 반드시 필요한 것이 아니다.

- `preferred weight` : 한개의 pod에 키가 다른 라벨이 2개가 있는 경우, 각 라벨에 부여된 weight값과 노드들의 자원을 고려하고 그 값을 계산해서 더 점수가 높게 나온 노드에 배치한다.

<br>

---

### **`Pod간 집중/분산`**

<br>

pod들을 노드에 배치할때 노드 스케쥴러를 사용하면 파드들간의 동시배치, 배타적 배치 등의 옵션을 사용할 수 있다.

- `Pod-Affinity` : master pod와 해당 파드와 함께 사용해야하는 slave pod가 있는경우, 같은 PV 호스트패스를 사용한다면 master pod가 최초 할당된 노드에 slave도 함께 생성된다.
- `Anti-Affinity` : Master와 Slave 구조일때 서로 다른 노드에 생성되게 해준다. 이는 같은 노드에 두개 pod가 같이 있으면 해당 node다운 시 서비스가 다운될 수 있는경우에 사용한다.

<br>

✓ **`Pod-Affinity`** 

해당 설정을 하면 type:web이라는 라벨을 가진 web pod(master pod)가 스케쥴러에 의해서 노드1에 할당이되면, server pod(slave nod)는 노드의 라벨을 확인하는것이 아닌 web pod의 라벨을 바라보고 배치되려한다.

또한 `topologyKey`를 사용하면 pod에서 해당옵션으로 설정한 라벨을 갖고있는 노드들 사이에서 master pod를 찾겠다고 설정할 수 있다.

이때, 만약 master pod가 slave pod가 찾고자하는 topologyKey가 아닌값에 들어갔을때는 pending상태가 되고, 조건이 맞을때까지 대기하게된다.

<br>

---

### **`Node 할당제한`**

노드 스케쥴러는 **`Toleration, Taint`** 기능을 사용하여 일반적인 경우에는 pod삽입이 되지않게, 조금은 특별하게 node 할당을 컨트롤할 수 있다. 이는 pod가 해당 노드를 직접 지정해도 할당이 되지않으며, toleration옵션이 달려있어야 할당이 된다. 이 옵션은 **GPU**와 같은 특별한 하드웨어 옵션을 가진 노드들을 이용해야하는 특정 파드를 배치할경우 유용하게 사용할 수 있다.

기본적으로 taint가 달린 노드에는 아무 pod나 들어갈 수 없고 toleration이 부합하는 pod만 들어갈 수 있지만, **toleration이 달려있어도 해당 node에 배치가 되는것은 별도의 설정이 필요하다.**

<br>

> toleration을 달아도 key, operator, value, effect 모두 일치해야 node배치가 가능하다.

- `noschedule` : 노드애 해당 설정이 있으면 다른 pod들이 배치되지않는다.(단, 기존에 이미 배치되었던 pod는 사라지지 않는다.)
- `preferNoSchedule` : 가급적 pod들이 배치되지않게끔하나, 정 배치할수 있는 노드가 없는경우 일반pod 배치를 허용해주는 경우이다.
- `noExecute` : 해당설정이 있으면 noSchedule과 다르게 일반pod 배치 금지 뿐만아니라 기존 pod가 삭제된다. 삭제를 원하지 않는 pod가 있는경우, 파드생성시 toleration effect에 noExecute 설정을 해주된다.

<br>

**`noSchedule`은 마스터노드에 기본적으로 달려있어서 pod를 만들때 마스터노드에는 배치되지 않도록 쿠버네티스 클러스터에 설정되어있다.**

또한, 노드에 장애가 발생하게되면 쿠버네티스는 해당 노드의 파드가 정상적으로 구동되지 않을수 있기때문에, noExecute태그를 자동으로 달아준다. 그럼 ReplicaSet은 연결된 pod가 없어졌기때문에 다른 노드에 파드를 생성하며 서비스를 만들게 해주는 구조이다.