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

### why Volume(볼륨)?

---
볼륨기능을 사용하지 않고 컨테이너를 운영하게되면 다음의 문제가 발생할 수 있다. 
- 첫째, 컨테이너에 문제가생겨 kubelet에의한 재기동이되면 컨테이너는 최초의 상태로 재생성되며 `기존에 갖고 있던 파일들이 유실`된다. 
- 둘째, 파드에서 컨테이너를 함께 실행할 때 컨테이너사이에 `파일을 공유해야 하는 경우`에 대응할 수 없다.


통상적으로 도커에서 제공하는 볼륨기능은 컨테이너당 하나의 볼륨 드라이버가 허용되고 매개 변수를 볼륨에 전달할 방법이 없는등, 다소 느슨한 기능을한다. (최근까지만해도 로컬에 백업이 가능한정도의 기능만 제공했다.) 반면, 쿠버네티스의 볼륨은 상당히 큰역할을 한다. 파드는 여러 볼륨을 동시에 사용할 수 있고, 컨테이너를 다시 시작해도 데이터가 보존된다.


컨테이너내의 프로세스는 도커이미지와 볼륨으로 구성된 파일시스템 뷰를 본다. 

---

> 참고
> [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/storage/volumes/)
> [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)
