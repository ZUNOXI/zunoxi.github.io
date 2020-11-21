---
layout: post
title: Kubernetes 13. Kubernetes의 Readiness Probe, Liveness Probe
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `Pod의 라이프사이클` Readiness Probe, Liveness Probe에 대한이해
  
- 목차
	- [`DaemonSet`](#dameonset)
	- [`job`](#job--cronjob)
	- [`cronjob`](#cronjob)
  
## Lifecycle for Pod 
---
쿠버네티스 클러스터에서 서비스 운영의 안정성과 무정지배포를 지원하기 위해 Pod의 라이프사이클 중 Readiness Probe, Liveness Probe에 대해 알아보고 이를 정리한다.

<br>

### **`Pod lifecycle`**

---


---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/controllers/daemonset/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)