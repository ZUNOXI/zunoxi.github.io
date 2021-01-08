---
layout: post
title: Kubernetes 8. Kubernetes의 Namespace
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `Namespace`에 대한이해
  
- 목차
	- [`Namespace`](#namespace)
	- [`Resource Quota`](#resource-quota)
	- [`Limit Range`](#limit-range)
  
## Namespace on k8s
---
쿠버네티스는 동일한 물리 클러스터를 기반으로 하는 여러 가상클러스터를 지원한다. 이런 `가상 클러스터를 네임스페이스`라고 한다. 이 네임스페이스에대해 알아보고 네임스페이스를 위한 ***Resource Quota와 Limit Range***에 대해서도 함께 알아본다.

<br>

### Why Namespace?

---

쿠버네티스의 네임스페이스는 `여러개의 팀이나, 프로젝트에 걸쳐서 많은 사용자가 있는 환경`에서 사용하도록 만들어졌다. 즉, 사용자가 거의없거나 소수인경우에는 네임스페이스 생성자체를 고려할 필요가 없는 ***선택적인 것***으로 이해하면 되겠다.

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

![그림1](/assets/img/devops/k8s/namespace/1.jpeg)


Resource Quota를 사용하면 Namespace마다 최대한계를 설정해서 ***파드자원이 한계를 넘을수 없도록*** 만들 수 있다. 위 그림은 각각의 네임스페이스가 사용할 수 있는 `메모리를 미리 설정하고 제한`한 예시이다.


Resource Quota를 설정한 경우에, 해당 Namespace에 들어오는 pod들은 반드시 스펙이 명시되어 있어야한다. `리미트를 초과하는 내용`이 들어오면 오류를 발생시킨다.

> 쿠버네티스의 버전이 업그레이드 되면서 제한할 수 있는 오브젝트의 범주가 늘어나고있다.

<br>

### Limit Range

---

네임스페이스에 들어올 `한 파드에 대한 리미트를 설정`할 수 있다. 즉, 네임스페이스에 들어올 수 있는 파드의 크기를 설정하고 파드가 들어올때마다 일일히 체크해주는 기능을한다.

![그림2](/assets/img/devops/k8s/namespace/2.jpeg)

위그림은 특정 Namespace의 `Limit Lange 메모리`를 대략 50정도로 잡았을때, 100을요구하는 pod가 배치되려할때 오류를 발생시키는것을 예시로 보여주는 그림이다.

`Resource Quota`가 ***전체 네임스페이스를 제한***하는 기능이라면, `Limit Range`는 ***파드각각을 검사***한다고 이해할 수 있다.

>Resource Quota와 Limit Range모두 네임스페이스 뿐만아니라 클러스터에도 설정할 수 있다.

<br>

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/namespaces/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)