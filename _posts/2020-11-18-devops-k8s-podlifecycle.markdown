---
layout: post
title: Kubernetes 12. Kubernetes의 Pod의 Lifecylce
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `Pod의 라이프사이클`에 대한이해
  
- 목차
	- [`DaemonSet`](#dameonset)
	- [`job`](#job--cronjob)
	- [`cronjob`](#cronjob)
  
## Lifecycle for Pod 
---
쿠버네티스 클러스터 오브젝트의 가장 작은 단위이자, 가장 중요한 Pod의 라이프 사이클에 대해 정리한다.

<br>

### **`Pod lifecycle`**

---

파드(Pod)는 아래 그림처럼 파드만의 라이프사이클이 있다. 


---

#### **`Pending`**

파드의 최초상태는 펜딩(pending)이다.

펜딩단계에서는 다음과 같은 몇가지 옵션을 설정할 수 있다.

#### Init Container

본 컨테이너가 가동되기전에 볼륨이나 보안세팅을 위해 사전세팅을하며 초기화시켜야 하는경우가 있다. 해당 경우에 init container를 파드 생성의 스크립트에 추가하면, 해당 설정이 본 컨테이너보다 먼저 실행이되고 그 작업이 성공적으로 끝날경우 True, 실패를 했을 경우 False를 반환한다.


---

#### Job 관련옵션

- `Completions` : 해당 설정을 하면 설정을 한 개수의 파드들을 순차적으로 만들어서 모든 생성작업이 끝나야 Job이 종료되게 설정할 수 있다.
- `Parallelism` : 해당 설정값만큼씩 파드가 생성된다. (ex) Completions : 6, parallelism :2 로 설정해놓으면 파드가 2개씩 3번 차례로 생성된다.)
- `activeDeadlineSeconds` : 설정한 시간후에 Job의 기능이 정지된다. 실행되고 있는 모든 파드는 삭제되고 실행안된상태로 대기중인 파드들도 구동계획이 삭제된다.
  - 해당경우는 10초안에 구동해야하는 서비스가 30초동안에도 정상구동이 되지않을시 Hang이 걸렸다고 판단 후 이를 정지하여 자원을 Save할 수 있다.

<br>



#### CronJob

앞서 설명한 Job은 보통 단일로 쓰이는것보다 CronJob설정을 통해 특정시간에 주기적으로 쓰이는 경우가 많다. 

> 예로 DB백업, 주기적인 어플리케이션 업데이트, 예약메시지 발송등에 사용된다.

CronJob의 Schedule 옵션은 Linux의 크론탭같은 기능을 제공한다.

- schedule : */1 * * * * => 해당경우에는 1초당 한개씩 Job이 만들어지고 Job에 설정된 대로 파드가 생성된다.
<br>


---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/controllers/daemonset/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)