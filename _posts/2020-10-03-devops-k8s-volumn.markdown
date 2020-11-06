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
컨테이너를 활용하는 쿠버네티스 환경에서 일반적으로 사용하는 도커이미지는 읽기 전용이기때문에 컨테이너내에 만들어지는 파일은 임시적이며, 컨테이너환경에서 실행되는 파일들은 언제든지 높은가능성으로 문제가 발생할 수 있다. 볼륨기능을 통해 우리는 이러한 현상을 예방할 수 있다.

<br>

### why Volume(볼륨)?

---
