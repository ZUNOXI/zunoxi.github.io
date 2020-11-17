---
layout: post
title: Kubernetes 11. Kubernetes의 Controller 3 - DaemonSet, Job, CronJob
subtitle: kubernetes의 기본개념
categories: devops
tags: devops kubernetes
comments: true
header-img: img/devops/k8s/kube.png
published: true
---

## 개요
> 쿠버네티스의 `DaemonSet, Job, CronJob`에 대한이해
  
- 목차
	- [`ReCreate`](#recreate)
	- [`Rolling Update`](#rolling-update)
	- [`Blue/Green`](#bluegreen)
	- [`Canary`](#canary)
  
## Deployment on k8s
---
앞선 포스팅에서 쿠버네티스의 `컨트롤러(Controller)`중 Deployment에 대해 알아봤다. 이번시간에는 컨트롤러의 나머지 종류인 DaemonSet, Job, CronJob에 대해서 알아보려한다.

<br>

### **`DameonSet`**

---

`데몬셋(DaemonSet)`은 다수의 노드들이 있을때, 리플리카셋(ReplicaSet)이 특정 노드에 파드를 만들어주는 역할을 한다면, **데몬셋은 모든 노드에 동일한 개수로 파드를 생성**해주는 역할을 한다. 이런기능으로 노드가 클러스터에 추가되었을때 데몬셋에 설정되어있는 해당 노드에 자동으로 파드가 추가된다.


데몬셋을 사용하는 대표적인 예시로 크게 3가지의 예를 들 수 있는데 `모니터링툴(ex)prometheus), 로그수집(ex)fluent), 스토리지(glusterFS)`가 그 예시이다. 위에 언급한 시스템들을 적절히 조합하면 각 노드의 상태를 점검 및 확인하고 자원들을 이용하여 네트워크 시스템을 구축할 수 있다.

<br>

데몬셋은 아래 몇가지 옵션값을 제공한다.

- 데몬셋을 설정할 때, 특정 노드에만 설정값을 적용시켜주고 싶을때는 ***`nodeSelector`*** 로 라벨을 달아주면된다. (그럼 그 라벨값을 갖고있는 노드들만 매칭이된다.)

- ***`hostPort`*** 설정 시 해당 노드로 들어온 트래픽을 파드로 직접 연결시켜주는 역할을 하는데 이는 서비스(Service)를 사용할 때, **externalTrafficPolicy**를 설정해주는것과 동일한 효과를 낸다.


> 일반적으로 데몬셋은 파드 사본이 항상 모든 호스트 또는 특정 호스트에서 실행되는 것이 중요하고, 다른 파드의 실행 이전에 필요한 경우에 사용한다.

<br>

---

#### **`JOB`**

Job은 특정 노드에 장애가 발생하여 해당 노드에 있던 파드들이 다운되고 이를 다른 노드에 재생성시켜주는 경우에 적용되는 controller이다. 일반적으로 ` ReplicaSet은 파드를 Recreate`시켜주고 프로세스의 움직임이 없을시 `restart`를 시켜주는데, `Job`은 파드를 생성후에 프로세스가 일을 하지 않으면 `멈춰져있는 상태를 유지`하게 한다는 특징이있다.

> 이는 아예 삭제되는것은 아니고 정지된 상태라 파드의 로그가 확인가능하고, 필요없는 파드라고 판단될 시 삭제가 가능하다.

여기서 기존 Deployment의 **Recreate** 같은 경우는파드를 다시 만들어주기때문에 파드의 이름과 아이피가 변경되지만, **restart**는 컨테이너만 재기동을 시켜주기때문에 앞서 언급한 정보들이 변경되지 않는다.



#### Job 관련옵션

- `Completions` : 해당 설정을 하면 설정을 한 개수의 파드들을 순차적으로 만들어서 모든 생성작업이 끝나야 Job이 종료되게 설정할 수 있다.
- `Parallelism` : 해당 설정값만큼씩 파드가 생성된다. (ex) Completions : 6, parallelism :2 로 설정해놓으면 파드가 2개씩 3번 차례로 생성된다.)
- `activeDeadlineSeconds` : 설정한 시간후에 Job의 기능이 정지된다. 실행되고 있는 모든 파드는 삭제되고 실행안된상태로 대기중인 파드들도 구동계획이 삭제된다.
  - 해당경우는 10초안에 구동해야하는 서비스가 30초동안에도 정상구동이 되지않을시 Hang이 걸렸다고 판단 후 이를 정지하여 자원을 Save할 수 있다.

<br>

---

#### **`CronJob`**

앞서 설명한 Job은 보통 단일로 쓰이는것보다 CronJob설정을 통해 특정시간에 주기적으로 쓰이는 경우가 많다. 

> 예로 DB백업, 주기적인 어플리케이션 업데이트, 예약메시지 발송등에 사용된다.

CronJob의 Schedule 옵션은 Linux의 크론탭같은 기능을 제공한다.

- schedule : */1 * * * * => 해당경우에는 1초당 한개씩 Job이 만들어지고 Job에 설정된 대로 파드가 생성된다.
<br>


---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)