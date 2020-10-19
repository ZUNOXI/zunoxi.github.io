---
layout: post
title: Linux(3) 하이퍼바이저(Hypervisor)에 대한 이해 
subtitle: Linux 3. 하이퍼바이저(Hypervisor)에 대한 이해
categories: infra
tags: infra linux/aix
comments: true
published: true
header-img: img/infra/linux/hyper/1.png
---

## 개요
> `하이퍼바이저(hypervisor)` 에대한 이해
  
- 목차
	- [`리눅스 메모리 사용량 확인`](#리눅스-메모리-사용량)
	- [`buffer? cache?`](#buffer-cache)
	- [`Top활용 프로세스별 메모리 확인`](#top활용-프로세스별-메모리-확인)
  
## why hypervisor?
---
리눅스와 도커를 공부하고 사용하다보면 하이퍼바이저라는 개념을 계속 듣게된다. 필자같은 경우, 인프라보다는 개발 공부를 더 많이 했었어서 그런지,, OS나 서버 가상화 같은 로우(low)단은 뭔가 익숙하지도 않고 공부하기 어렵다는 생각이 들어서 개념만 대충 공부하고 넘겼었다.

<br>

---

### **1\. 하이퍼바이저란??**

위키백과는 하이퍼바이저를 아래와 같이 정리하고 있다.

> 하이퍼바이저(Hypervisor) : 호스트 컴퓨터에서 다수의 운영체제를 동시에 실행하기 위한 논리적 플랫폼. `가상화 머신 모니터 또는 가상화 머신 매니저`이며 약어로 VMM(Virtual Machine Monitor)이라고 한다.

라고 말하는데, 하이퍼바이저라는 개념자체를 처음 접하는 사람은 쉽게 이해하기 힘들다. 나처럼..(!)  
(물론 OS나 서버 가상화에 해박한 지식을 가지고 있다면 읽을 필요가 없는 포스팅임을 먼저 밝힌다.)

필자가 책과 구글링을 통해 정리한 하이퍼 바이저의 개념은 다음과 같다.

---

먼저, 하이퍼바이저는 단일 하드웨어에서 여러 다른 가상 머신을 호스팅 할 수 있는 프로그램이라고 할 수 있다. 일반적으로 가상 머신하면 VMware나 VirtualBox가 생각나는데, 이도 하이퍼바이저를 응용한 기술이다.

### **2\. 하이퍼바이저 유형**

하이퍼바이저는 크게 두 유형으로 나눌 수 있다.

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/hyper/2.png)

**유형 1. Native or Bare-Metal **

하이퍼바이저가 호스트의 하드웨어위에서 직접 실행되며 하드웨어를 제어하고 게스트 가상머신을 관리한다. 기존의 OS가 하드웨어를 제어하고 그 위의 얹어진 프로그램들을 관리했던것 처럼, 하이퍼바이저는 하드웨어 제어 > 위에 얹어진 게스트 OS 제어, 이런식으로 구성된다고 할 수 있다.

게스트 OS 중 하나에서 문제가 발생해도 하드웨어에서 실행 중인 다른 게스트 OS에 영향을 미치지 않는 특징이 있다. Virtual Box나 Vmware Workstation을 이용했던 필자는 해당 개념이 처음에 이해가 잘 안됐는데, 실무에서 vmware의 vSphere를 사용하며 대략적인 감을 잡고 있다.

해당 프로그램을 보면 하드웨어에 별도 OS 없이 vSphere가 가상머신들을 생성하고 제어할 수 있는 기능을 제공해준다.

(+ 클러스터를 위한 별도의 가상머신을 둬서 클러스터링도 가능하게 할 수 있다.)

[https://www.vmware.com/kr/topics/glossary/content/hypervisor.html](https://www.vmware.com/kr/topics/glossary/content/hypervisor.html)

이와 유사한 프로그램들로 'Xen, Oracle VM Server for SPARC, Oracle VM Server fo x86'가 있다.

---

**유형 2. hosted**

하이퍼바이저가 시스템의 다른 응용프로그램과 마찬가지로 일반적인 OS 위에서 실행된다. 게스트 OS는 호스트에서 프로세스로 실행되는 반면 하이퍼 바이저는 게스트 OS와 호스트 OS를 분리한다. 하이퍼바이저는 운영에 있어 호스트 운영체제에 전적으로 의존한다는 특징이 있다. 우리가 일반적으로 VM하면 생각나는 VMware Workstation, VMware Player, VirtualBox 등이 그 예 이다.

---

**3\. 컨테이너와 하이퍼바이저**

컨테이너 기술은 일반적으로 가상머신보다 단일 물리적 서버에 더 많은 애플리케이션을 배치 가능하다. 기존 가상화 기술보다 가볍고, 이식성이 뛰어나다. Docker가 그 대표적인 예 이다. 그러나 반드시 컨테이너가 항상 유리한 것은 아니다. 

보안문제를 보자면 컨테이너는 하나의 OS위에서 해당 OS를 공유하는 시스템이고 가상머신은 응용프로그램 뿐만아니라 각각의 OS도 격리하기 때문에 치명적인 보안문제가 발생했을때 상대적으로 가상머신이 더 안전하다고 할 수 있다.

이에 전문가들도 컨테이너 환경에서만 시스템을 구성하는 것 보다 기존 하이퍼바이저를 이용한 가상머신과의 혼용을 권장하고 있다. 컨테이너 시스템과 관련해서는 아래 포스팅을 참고하면 좋을것 같다.

[https://zunoxi.tistory.com/36?category=871487](https://zunoxi.tistory.com/36?category=871487)

[

Cloud #2. MSA(Microservices Architecture)와 Monolithic

MSA : Micro Service Architecture, '사전적으로는 애플리케이션을 느슨히 결합된 서비스의 모임으로 구조화 하는 서비스 지향 아키텍처 스타일의 일종인 소프트웨어 개발 기법' 이라고 합니다. 간단하게

zunoxi.tistory.com



](https://zunoxi.tistory.com/36?category=871487)