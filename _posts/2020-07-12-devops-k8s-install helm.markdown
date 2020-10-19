---
layout: post
title: Devops 1. helm 설치하기
subtitle: devops 파이프라인 구축하기
categories: devops
tags: devops kubernetes
comments: true
published: true
header-img: img/devops/k8s/helm/logo.jpeg
---

## 개요
> `Git push` 부터 `쿠버네티스` 파드 배포 까지 파이프라인 구축 &#10102;단계
  
- 목차
	- [`DevOps구조`](#devops구조)
	- [`Helm?`](#helm)
	- [`Why Helm`](#why-helm-)
	- [`How to install`](#how-to-install-)
  
## Helm??
---
헬름은 쿠버네티스 차트를 관리하기 위한 도구이다. 차트는 사전 구성된 쿠버네티스 리소스의 패키지라고 할 수 있다. 즉, 쿠버네티스 패키지 관리 도구이다. `Helm 설치로 필요한 프로그램 패키지 파드 배포`

<br><br>

### DevOps구조

---

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/jenkins/install/structure.png)

이번 포스팅에서는 필요한 프로그램 패키지를 쿠버네티스상의 `파드`로 배포하기 위해 Helm을 설치하는 과정에 대해 설명하려한다.

<br><br>

### Why Helm ??

---

일반적으로 `쿠버네티스`에서 특정 파드를 만들려면 파드에대한 yaml 파일이 필요하고, 
또 이를 배포시켜줄 서비스와, 파드의 생성주기를 관리해줄 레플리카셋에 대한 설정이 필요하다.

이를 일일히 설정해주는것은 귀찮을 뿐더러 설정과정에서 오류가 많이 일어날수있다. 여기서 Helm을 사용하면 특정 프로그램을 배포하고 싶은 경우, 

이미 누군가가 여러개의 파드, 서비스, 레플리카셋을 세트화 시켜 놓고 `helm hub`상 공유한 것을 chart로 다운받아 간단히 원하는 부분만 수정하고 실행시켜서 배포 할 수 있다.

docker hub에 내가 만든 이미지가 올라가있다면, 이를 이용해서 helm chart를 수정하는것도 가능하다.  _**즉, 배포가 간편하다는 뜻이다.**_

<br><br>

### How to install ??


---

**(1). helm 실행 환경을 다운로드**

> `$ curl https://raw.githubusercontent.com/helm/helm/master/scripts/get > get_helm.sh `

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/helm/1.png)

<br>

**(2). helm 실행 파일에 권한부여**

> `$ chmod 700 get_helm.sh`

> `$ ./get_helm.sh`

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/helm/2.png)

<br>

**(3). helm 주입**

> `$ helm init`

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/helm/3.png)

<br>

**(4). kubernetes 상태 확인**

> `$ kubectl get service,deployment,pod -n kube-system`

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/helm/4.png)

<br>

**(5). helm 버전 확인**

> `$ helm version`

![그림6](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/helm/5.png)

<br>


이제 helm을 이용해서 gitlab과 jenkins를 쿠버네티스상에서 배포해보자.

[`다음 포스팅에서`]


### 참고 

---

[`https://tech.osci.kr/2019/11/23/86027123/`](https://tech.osci.kr/2019/11/23/86027123/)