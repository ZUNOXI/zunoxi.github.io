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
	- [`Namespace`](#namespace)
	- [`Resource Quota`](#resource-quota)
	- [`Limit Range`](#limit-range)
  
## Replication Controller on k8s
---
쿠버네티스의 `컨트롤러(Controller)`는 서비스를 관리하고 운영하느것에 큰 도움을 준다. 이번 포스팅에서는 컨트롤러, 그리고 조금더 진화한 형태의 `레플리케이션 컨트롤러(Replication Controller)`에 대해서 알아본다.

<br>

### Controller?

---

쿠버네티스에서 컨트롤러는 클러스터의 상태를 관찰한다음, 필요한 경우에 생성 또는 변경을 요청하는 컨트롤루프(시스템 상태를 조절하는 종료되지 않는루프)이다.

<br>

### Namespace

---

쿠버네티스의 클러스터, 네임스페이스들은 `자원을 서로 공유`하면서 사용한다. 또한, 한 네임스페이스안에 같은 타입에 오브젝트들간에는 중복이 되지않는다. ***같은 파드의 이름역시 중복해서 만들 수 없다.***

타 네임스페이스와 자원이 분리되어서 관리된다. `대부분의 자원들은 해당 네임스페이스안에서만` 사용할 수 있다.(노드나 PV처럼 공용으로 사용되는것도 있다.)

> 네임스페이스가 다른 파드들끼리 네트워크로는 연결이 가능하다.

<br>

### Resource Quota

---

이때 한 Namespace에 있는 파드가 이 클러스터의 남은 자원을 모두 사용해버리면 다른 파드입장에서는 쓸 자원이 없어서 자원이 필요할 때 문제가 발생할 수 있다. 이때 사용되는것이 `Resource Quota`이다.

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/namespace/1.jpeg)


Resource Quota를 사용하면 Namespace마다 최대한계를 설정해서 ***파드자원이 한계를 넘을수 없도록*** 만들 수 있다. 위 그림은 각각의 네임스페이스가 사용할 수 있는 `메모리를 미리 설정하고 제한`한 예시이다.


Resource Quota를 설정한 경우에, 해당 Namespace에 들어오는 pod들은 반드시 스펙이 명시되어 있어야한다. `리미트를 초과하는 내용`이 들어오면 오류를 발생시킨다.

> 쿠버네티스의 버전이 업그레이드 되면서 제한할 수 있는 오브젝트의 범주가 늘어나고있다.

<br>

### Limit Range

---

네임스페이스에 들어올 `한 파드에 대한 리미트를 설정`할 수 있다. 즉, 네임스페이스에 들어올 수 있는 파드의 크기를 설정하고 파드가 들어올때마다 일일히 체크해주는 기능을한다.

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/namespace/2.jpeg)

위그림은 특정 Namespace의 `Limit Lange 메모리`를 대략 50정도로 잡았을때, 100을요구하는 pod가 배치되려할때 오류를 발생시키는것을 예시로 보여주는 그림이다.

`Resource Quota`가 ***전체 네임스페이스를 제한***하는 기능이라면, `Limit Range`는 ***파드각각을 검사***한다고 이해할 수 있다.

>Resource Quota와 Limit Range모두 네임스페이스 뿐만아니라 클러스터에도 설정할 수 있다.

<br>

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/namespaces/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)