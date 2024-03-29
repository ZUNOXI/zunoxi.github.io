---
layout: post
title: Kubernetes 10. Kubernetes의 Contorller 2 - deployment
subtitle: kubernetes의 deployment ReCreate, Rolling Update, Blue/Green, Canary 
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---


해당 포스팅은 [https://zunoxi.tistory.com/87?category=950191](https://zunoxi.tistory.com/87?category=950191) 으로 이전되었습니다.

<!--

## 개요
> 쿠버네티스의 `Deployment`에 대한이해
  
- 목차
	- [`ReCreate`](#recreate)
	- [`Rolling Update`](#rolling-update)
	- [`Blue/Green`](#bluegreen)
	- [`Canary`](#canary)
  
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

---

#### **`ReCreate`**

ReCreate 디플로이먼트 방식은 기존에 운영중이던 App의 **v1버전 파드를 삭제**한 뒤 서비스자원이 삭제되었을때, **v2버전의 파드를 새로 생성**하는 방식이다.

해당 방식은 _`서비스의 다운타임이 발생`_ 한다는 단점이 있다.

<br>

![그림1](/assets/img/devops/k8s/deployment/1.jpeg)

<br>

---

#### **`Rolling Update`**

Rolling Update 방식은 소프트웨어를 업데이트할때, v1버전이 운영되고 있는 도중에 **v2버전의 파드를 미리 만들어주는 방식이다**. 이는 v2도 생성 이후 함께 서비스 중이기때문에, 누군가는 v2로 접속하여 서비스를 사용할 수 도 있다. 

이때 v2파드가 하나 생성될때 이상이 없으면 v1파드 하나가 삭제된다. 이런 방식으로 기존 파드가 운영되었던 개수만큼 **v2파드가 생성과 v1파드의 삭제가 반복**된다.

Rolling Update 방식은 다운타임이 생기지는 않지만(zero downtime) v2서비스를 미리 만들면서 _`자원의 할당량이 늘어난다`_ 는 특징이 있다.

<br>

![그림2](/assets/img/devops/k8s/deployment/2.jpeg)

<br>

![그림3](/assets/img/devops/k8s/deployment/3.jpeg)

<br>

---

#### **`Blue/Green`**

Blue/Green 방식은 디플로이먼트가 자체적으로 제공하는 기능은 아니고, **리플라카셋과 같이 리플리카스를 관리하는 모든 컨트롤러에서 사용이 가능**하다. 

이는 컨트롤러를 하나 거 만들어서 v2버전에 대한 파드를 만들고 서비스의 라벨도 v2로 변경한 후 서비스에서 **기존 파드와의 관계는 끊어버리고 v2버전에 대한 파드들과 연결**하는 구조이다.

해당 방식은 순간적으로 전환되기때문에 zero downtime이며 만약 v2버전 파드들과 연결되었을때 문제가 발생한다면 서비스의 라벨만 v1으로 변경해주면 되기때문에 _`롤백이 간단하다`_ 는 특징이 있다.

> 일반적으로 가장 많이 사용하는 방식이지만 같은 구조의 컨트롤러를 두개 만들어야한다는 점에서 **쿠버네티스의 자원을 2배씩 먹게된다**는 단점도 있다.


<br>

![그림4](/assets/img/devops/k8s/deployment/4.jpeg)

<br>

---

#### **`Canary`**

카나리(Canary)방식의 단어 어원부터 짚고 넘어가자면 카나리는 새의 종류로 1초에 심박수가 17번이나 뛰어 석탄광산에서 유독가스 누출의 위험을 알리는데 사용되었다고 한다.

그래서 카나리 방식의 배포방식은 카나리같은 `실험체를 통해 위험요소를 감지하고 위험요소가 없다면 배포`를 실시하는 방법이라고 이해할 수 있겠다.

카나리 방법은 크게 **불특정다수에게 서비스 테스트**하는경우와 **접근주소를 다르게하여 서비스 테스트**하는경우로 나뉜다.

- `불특정다수 테스트` : v1파드가 type:app 라벨이 붙어져있는 상태에서 v2 테스트용으로 컨트롤러를 만들때 리플리카스를 1로 설정하여 v2에 대한 파드를 하나 만들고, 이때 동일하게 type:app에 대한 라벨을 만들어 type:app에 대해 서비스를 제공하고있는 하나의 서비스에 `기존 v1과 v2가 함께 연결`되게한다.
  > 이방법은 v1과 v2에 대한 접근이 모두 가능하게 만들어서 테스트하는 방법이다.
- `특정경로 테스트` : v1용도의 서비스와 별도로 v2테스트용으로 서비스를 만들고 Ingress Controller를 연결하여 url에 대한 접근 주소로 서비스별 연결을 다르게 할 수 있게 설정한다. 
  > 테스트기간동안에 이상이 없다면 기존에 사용하던 pod v1과 관련된 자원들을 삭제하고 v2를 사용할 수 있다.

<br>

![그림5](/assets/img/devops/k8s/deployment/5.jpeg)

<br>

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)

-->
