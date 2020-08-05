---
layout: post
title: devops(2) Kubernetes에 Gitlab서버 설치
subtitle: jenkins를 활용한 파이프라인 구축
categories: devops
tags: devops jenkins
comments: true
published: true
header-img: img/devops/jenkins/install/jenkins.jpg
---

## 개요
---
> Git push 부터 쿠버네티스 파드 배포 까지의 파이프라인 구축 첫단계

[`Git-hook을 통한 Gitlab과 jenkins 연동`]




### 구조

![그림1](https://zunoxi.github.io/assets/img/devops/jenkins/install/jenkins.jpg)

이번 포스팅에서는 Gitlab과 jenkins와의 연동에 대해 확인한다.

---


### 전제 조건

---

먼저 쿠버네티스가 설치되어 있고, helm 역시 설치되어 있는 상태에서 

gitlab 저장소를 쿠버네티스에 배포 할 수 있게 구성한다.


[`초기화면 : 쿠버네티스 대시보드`]

![그림2](https://zunoxi.github.io/assets/img/devops/k8s/gitlab/dashboard.png)



### gitlab helm 배포

---







