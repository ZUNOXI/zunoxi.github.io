---
layout: post
title: Kubernetes 6. Kubernetes의 Volume
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `volume`에 대한이해
  
- 목차
    - [`Pod(파드)란`](#pod파드란)
    - [`Service(서비스)란`](#service서비스란)
  
## Volume in k8s
---
컨테이너를 활용하는 쿠버네티스 환경에서 일반적으로 사용하는 도커이미지는 읽기 전용이기때문에 컨테이너내에 만들어지는 파일은 `임시적`이며, 컨테이너환경에서 실행되는 파일들은 언제든지 `높은가능성으로 문제가 발생`할 수 있다. 우리는 Volume기능을 통해 이러한 현상을 예방할 수 있다.

<br>

### Why Volume(볼륨)?

---
볼륨기능을 사용하지 않고 컨테이너를 운영하게되면 다음과같은 문제가 발생할 수 있다. 
- 첫째, 컨테이너에 문제가생겨 kubelet에의한 재기동이되면 컨테이너는 최초의 상태로 재생성되며 `기존에 갖고 있던 파일들이 유실`된다. 
- 둘째, 파드에서 컨테이너를 함께 실행할 때 컨테이너사이에 `파일을 공유해야 하는 경우`에 대응할 수 없다.


통상적으로 _**도커에서 제공하는 볼륨**_ 기능은 `컨테이너당 하나의 볼륨 드라이버`가 허용되고 매개 변수를 볼륨에 전달할 방법이 없는등, **다소 느슨한 역할**을 한다. (최근까지만해도 로컬에 백업이 가능한정도의 기능만 제공했다.) 

<br>

반면, _**쿠버네티스의 볼륨**_ 은 상당히 중요한 역할을 한다. 파드는 여러 볼륨을 동시에 사용할 수 있고, 컨테이너를 다시 시작해도 데이터가 보존된다. 컨테이너내의 프로세스는 도커이미지와 볼륨으로 구성된 파일시스템 뷰를 보며, 모든 볼륨은 이미지 내에 `지정된 경로에 마운트`된다. 


---

Volume의 대표적인 적용방식에 대해 기술한다.

### **1. emptyDir**

`emptyDir`은 컨테이너들끼리 데이터를 공유하기 위해 볼륨을 사용하는것이다. 따라서, 처음에는 `볼륨이 비어있기때문`에 emptyDir이라고 명명한다. 볼륨은 파드안에 생성이 되고 pod생성시 만들어지며 pod 삭제시에 없어진다는 특징이있다.

> 일시적 사용목적이있는 데이터보관할때 사용한다.

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/volume/1.png)

허접한 그림솜씨...

<br>

---

### **2. hostPath**

`hostPath`는 노드안에 볼륨을 만들고 각 파드들이 path로 만운트를 해서 사용하기 떄문에 파드가 사라져도 데이터가 날아가지 않는다는 특징이있다.

단, 특정 파드가 죽어서 재생성될때 해당 노드에서 재생성된다는 보장이 없기 때문에 자신이 원래 매칭되어있던 노드의 볼륨을 참고하지 못하는 경우가 발생한다.

이를 해결하기 위해 노드를 새로 만들때마다 기존 노드 볼륨에 마운트를 해주는 방법이 있다. (해당방법은 관리자 직접해줘야하는 기능이다.)

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/volume/2.png)

<br>

> 이는 실수가 발생할 여지를 주기때문에 바람직한 방법은 아니다.

각각의 노드에는 기본적으로 각 노드 자신을 위해 시스템 파일들이 있는데 파드 자신이 할당되어있는 호스트의 데이터를 읽거나 쓸때는 이 hostPath를 사용하면 된다.

여기서 volume path를 생성할 때는 노드에 `path로 잡을 경로`가 미리 만들어져 있어야한다.

<br>

---

### **3. PVC/PV**

- PVC(Persistent Volume Claim, 이하 PVC)
- PV(Pesistent Volume, 이하 PV)

PVC/PV로 볼륨을 운영하는 방법은 User(사용자)와 Admin(관리자)의 역할을 구분해서 운영하기 위한 방법이다.

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/volume/3.png)

User가 파드를 생성하고 필요한 볼륨크기에대해 PV 생성을 `Admin에게 요청`하면(PVC행위)를 하면 Admin이 PV를 할당해주는 개념이다. 

이 방식을 사용하면 user와 admin의 역할구분이되는것 뿐만아니라 위에서 언급했듯이 `파드가 재생성될때` 기존에 사용하던 노드가 아닌 다른 노드에 생성될 경우도 있는데, 이때 PV설정이 되어있다면 _**자동으로 데이터가 있는 볼륨이 컨테이너가 구동되는 노드로 옮겨 붙는 방식**_ 으로 사용 가능하다.

PV를 사용하기전에 먼저 PV를 만들어주어야하는데 이를 프로비저닝이라고 부른다.

> 그 PV를 생성하는 위치는 Local Disk일수도있고 AWS, GCP등의 클라우들 플랫폼을 이용하는 가상공간일 수 도있다.

`퍼시스턴트 볼륨`관련된 설명은 다음 링크의 블로그 포스팅을 참고하면 도움이 더욱 될 것같다.
[`링크`](https://arisu1000.tistory.com/27849)



<br>

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/storage/volumes/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)
