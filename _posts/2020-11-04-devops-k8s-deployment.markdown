---
layout: post
title: Kubernetes 10. Kubernetes의 Deployment
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `Deployment`에 대한이해
  
- 목차
	- [`Controller`](#controller)
	- [`Replication Controller`](#replication-controllerrc)
  
## Deployment on k8s
---
쿠버네티스의 `컨트롤러(Controller)`중 한 서비스가 운영중일때, 해당 서비스를 업데이트 해야할 경우 Deployment 컨트롤러를 사용하여 좀더 손쉽게 업데이트 할 수 있다.

<br>

### Deployment

---

디플로이먼트는 쿠버네티스 컨트롤러의 한 종류로서 `특정서비스를 업데이트하며 배포`할때 유용하게 사용하는 컨트롤러이다. 이 컨트롤러라는 개념은 [링크](https://zunoxi.github.io/devops/2020/10/28/devops-k8s-replication/)를 참조하여 이해를 먼저 하면 좋을것 같다. 쉽게 말해서 디플로이먼트는 파드(Pod)와 리플리카셋(ReplicaSet)에 대해 명시적으로 선언하는 컨트롤러이다. 

<br>

디플로이먼트의 종류는 아래와 같이 분류된다.
  
> 설명은 `v1 버전의 app`을 `v2버전으로 업그레이드` 해야하는 경우를 예시로 들어 설명하려한다. 

<br>

#### **`ReCreate`**

ReCreate 디플로이먼트 방식은 기존 App의 **v1버전 파드를 삭제**한 뒤에 서비스자원이 삭제되었을때, **v2버전의 파드를 새로 생성**하는 방식이다.

해당 방식은 _`서비스의 다운타임이 발생`_ 한다는 단점이 있다.

<br>

#### **`Rolling Update`**

Rolling Update 디플로이먼트 방식은 업데이트할 **v2버전의 파드를 미리 만들어준다**. 이는 v2도 생성 이후 함께 서비스 중이기때문에, 누군가는 v2로 접속하여 서비스를 사용할 수 도 있다. 

이때 v2파드가 하나 생성될때 이상이 없으면 v1파드 하나가 삭제된다. 이런 방식으로 기존 파드가 운영되었던 개수만큼 **v2파드가 생성과 v1파드의 삭제가 반복**된다.

Rolling Update 방식은 다운타임이 생기지는 않지만 v2서비스를 미리 만들면서 _`쿠버네티스의 자원을 2배씩 사용해야한다`_ 는 특징이 있다.

<br>

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicationcontroller/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)