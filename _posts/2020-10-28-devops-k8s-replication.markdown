---
layout: post
title: Kubernetes 9. Kubernetes의 Replication Controller
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `Replication Controller`에 대한이해
  
- 목차
	- [`Controller`](#controller)
	- [`Replication Controller`](#replication-controllerrc)
  
## Replication Controller on k8s
---
쿠버네티스의 `컨트롤러(Controller)`는 서비스를 관리하고 운영하느것에 큰 도움을 준다. 이번 포스팅에서는 컨트롤러, 그리고 그 종류중 하나인 `리플리케이션 컨트롤러(Replication Controller)`에 대해서 알아본다.

<br>

### Controller?

---

쿠버네티스에서 컨트롤러는 클러스터의 상태를 관찰한다음, 필요한 경우에 생성 또는 변경을 요청하는 `컨트롤루프`(시스템 상태를 조절하는 종료되지 않는루프)이다. 컨트롤러를 이용하면 일일히 파드 하나하나를 관리하지 않아도 된다.

컨트롤러는 아래와 같은 4가지의 기능을 제공한다.

- `Auto Healing` : pod가 다운되거나 혹은 pod가 있는 노드가 다운되는 경우 장애가 발생할때, 컨트롤러는 이를 즉각적으로 인지하고 다른 노드에 pod를 재생성해준다.
  
	![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/controller/1.jpeg)
	<br>

- `Auto Scaling` : 파드의 리소스가 리미트 상태에 이르렀을때, 파드를 하나 혹은 그 필요정도를 계산하여 추가적으로 만들어주는 기능. (파드의 리밋고려 자원 추가 생성)

	![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/controller/3.jpeg)
	<br>

- `Software Update` : 여러파드에 대해 업그레이드해야할 경우 한번에 업그레이드할 수 있도록 지원한다.

	![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/controller/2.jpeg)
	<br>

- `Job` : 일시적으로 특정 작업을 해야하는 경우, 필요한 순간에 임시적으로 파드를 만들어서 해당작업을 이행하고 삭제한다.
  
	![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/controller/4.jpeg)
	<br>
 
쿠버네티스의 컨트롤러의 종류는 ***Replication Controller, ReplicaSet, DaemonSet, Job, Deployment***등이 있다. 이번 포스팅에서는 이중 `Replication Controller`에 대해 알아본다.


<br>

### Replication Controller(RC)

---

리플리케이션 컨트롤러는 쿠버네티스 컨트롤러중의 초창기부터 사용했던 `기본 컨트롤러`로서 ***지정한 숫자만큼 파드가 클러스터내에서 실행***되도록 관리하는 역할을 한다.

사실, 리플리케이션 컨트롤러는 Deprecated되었고, 지금은 `리플리카셋(ReplicaSet)`으로 대체되었다. 그러나 아직까지 리플리케이션 컨트롤러를 사용하고있고 사용중 ReplicaSet으로 변경하는것이 어렵지 않으므로 필자에게 익숙한 리플리케이션 컨트롤러에 대해 설명하려한다.

> 레플리케이션은 보통 rc로 축약해서 표현하며, Kubectl에서 숏컷으로 사용된다.

<br>

리플리케이션 컨트롤러는 다음 기능들을 제공한다.
- `Replicas` : replicas의 설정 개수만큼 파드의 개수가 관리되고, 파드가 삭제되면 설정된 개수만큼 파드가 재생성되어 전체적인 설정개수를 만족시킨다. 
- `Template` : 컨트롤러와 파드는 서비스와 셀렉터로 연결이 된다. 템플릿으로 파드의 내용을 넣고, 이후 파드 다운시에 파드가 재생성되며 템플릿에 명시되어있는 `파드의 내용대로` 만들어진다. 이러한 특성을 사용해서 ***앱에 대한 업그레이드가 가능***하다. 즉, 기존에 연결되었던 파드를 다운시키면 연결된 템플릿이 이를 감지하고 수동으로 업그레이드 시킬 수 있다는 뜻이다.
  > replication을 만들때 replicas의 수치를 설정하되 pod를 별도로 연결하지 않는다면, 템플릿의 설정된 파드를 이용해서 그 설정 개수만큼 자동으로 파드를 생성한다.
- `Selector` : 이는 ReplicaSet에만 있는 기능으로 일반적으로 Replication Controller는 라벨과 셀렉터가 일차하는 오브젝트들을 매칭하게 되어있지만, ReplicaSet같은 경우는 **`matchLabels와 matchExpression`** 으로 나누어 추가 기능을 제공한다.
	> matchLabels는 기존 Replication Controller의 방식과 동일하나 matchExpressions는 key값으로도 pod를 매칭할 수 있다는 차이점이 있다.

	- matchExpressions는 Exist, DoseNotExist, In, NotIn 4가지의 종류로 설정할 수 있다. 관련내용은 [링크](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/labels/)를 참고한다.

<br>


---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicationcontroller/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)