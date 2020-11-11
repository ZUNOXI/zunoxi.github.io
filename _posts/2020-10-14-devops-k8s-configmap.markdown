---
layout: post
title: Kubernetes 7. Kubernetes의 ConfigMap, Secret
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `ConfigMap, Secret`에 대한이해
  
- 목차
    - [`볼륨기능`](#why-volume볼륨)
    - [`EmptyDir`](#1-emptydir)
    - [`hostPath`](#2-hostpath)
    - [`Persistent Volume`](#3-pvcpv)

## ConfigMap and Secret
---
`사소한 설정값` 몇개 때문에, 큰 용량의 이미지를 별도로 관리한다는것은 매우 비효율적인일이다. 따라서 이런 간단한 정보는 컨테이너 `내부에서 외부 정보를 참조`할 수 있게 해주는 기능이있다. 그것이 바로 ***ConfigMap과 Secret***이다.

<br>

### ConfigMap

---
우리는 종종 개발환경과 상용환경이 달라서 생기는 몇가지 문제들을 직면한다. 예를들어, `SSH설정과 같은 key값에 대한 차이`로 개발계에서 테스트하는 이미지와 운영계에서 운영할 이미지를 별도로 만들어야될 수 있겠다.

이때 ***ConfigMap***을 사용하면 이미지가 구동되는 컨테이너 전체의 설정을 바꾸는 것이 아니라, ConfigMap의 설정만 바꾸고 컨테이너에서는 해당 설정값들을 ConfigMap에 있는 값들을 참조하게 설정할 수 있다. 즉, `같은 이미지파일로` 개발계와 운영계의 컨테이너를 구동시킬 수 있다. 

> 위 내용처럼 `Secret`도 동일한 원리로 사용된다.

***ConfigMap***은 `키(key)와 상수(Value)`로 구성되어있다. 일반적으로 컨테이너내의 환경변수에 세팅을 할 수 있다.

<br>

### Secret
---

***Secret***도 `키(key)와 상수(Value)`로 구성되어있다. Secret은 보안과 관련된 값을 저장하는 기능을한다. 패스워드나 키값, 그리고 value를 넣을때 `Base64 인코딩`을 해서 넣어야하는 것이 ConfigMap과의 차이이다. 

> 단, pod로 주입이 될때는 자동으로 디코딩이 되어서 대시보드환경에서는 다 보여지게된다.

또한, Secret은 보통 메모리에 저장이된다. 메모리에 저장되는것이 보안에는 더 좋지만 Secret을 너무 많이 만들면 메모리 성능에 영향으 줄 수 있다는점도 주의해야한다.


---
> 참고
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)