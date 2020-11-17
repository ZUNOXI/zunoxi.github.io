---
layout: post
title: Cloud 2. CNA(Cloud Native Architecture)에 대한 이해
subtitle: 클라우드 네이티브 아키텍처에 대한 이해
categories: cloud
tags: cloud cloud
comments: true
published: true
---

## 개요
> 클라우드 환경에서 최적화된 MSA를 구현하기 위한 CNA에 대한 이해
  
- 목차
	- [`MSA 구조`](#msa의-구조-및-구성효과)
	- [`Kafka의 역할`](#kafka)
	- [`Docker의 사용`](#docker)
	- [`Kubernets 도입`](#kubernetes)
  
## Cloud Native Architecture
---
최근 기존 온프레미스 환경의 인프라에서 클라우드환경으로 시스템 운영환경이 변해가며 클라우드 친화적 개발환경인 `클라우드 네이티브 아키텍쳐(이하 CNA)`에 대한 관심이 높아졌다. 특히나 클라우드가 각광받음과 더불어 `MSA, 컨테이너` 등 이 핫한 기술로 여겨지고 있다. 이번 포스팅에서는 CNA에 대한 개념을 잡고 CNA의 핵심인 여러 개념들에대해 알아보려한다. (너무 거창한 오프닝같다..🤨)


<br>

	CNA는 원할한 시스템운영을 위해 DevOps과정이 수반된다. 본 포스팅에 DevOps관련된 내용은 생략했으며, 이는 기존에 포스팅했던 CI/CD 관련 포스팅들을 참고하면 좋을것 같다.

### CNA 설계, 개발 단계

---

#### **`DDD(Domain-Driven Design)`**

<br>

사용자가 관심을 갖고 있는것, 사용중인것을 모두 도메인(Domian)이라고 한다. `OOP 프로그래밍`에서 Object(객체)는 추상화 또는 구체화할 수 있는 특정요소만을 표현하는 반면, 도메인은 사용자가 사용하는 모든것을 설명할 수 있다.

도메인주도 디자인이라는것은 즉, 개발시 도메인이 중심이 되는 개발방식이며 **높은 응집도, 낮은 결합도**를 추구 (Loose Coupling, High Cohesion)

<br>

___

#### **`Event Driven Architecture - 소프트웨어 아키텍처 및 모델`**

<br>

도메인 모델에서 어떤 일이 일어났을때 이를 대표하는 이벤트를 `도메인 이벤트`라고 한다. 도메인 이벤트를 전파시키면서 자신의 해야할 일을 하고, 그 이벤트를 받은 서비스가 자체적으로 다른 이벤트를 만들어가는것을 이벤트 ***`드리븐(Event Driven)`*** 이라고 말한다. (특정행동이 순서에 따라 발생하는것이 아닌 어떤일에 대한 반응으로 동작하는 디자인 패턴)

	Event Driven 방식은 MSA가 적용된 시스템에서 이벤트 발생 시 해당 이벤트 로그를 보관하고 이를 기반으로 동작하며, 비동기 통신(Async)을 통해 시스템 내 통합을 수행하는 아키텍처이다.

<br>

`MSA`를 구현함에 있어 등등 핵심적인 키워드는 ***`기존시스템을 쪼개는것`*** 에 중점이 되는데, **이때 가장 어려운것이 Database를 서비스 단위로 쪼개서 사용하는것**이다. 이는 엔터프라이즈에서 데이터가 중요자산이고 shared 관계형 데이터 베이스 장점 사용불가인 이유로 `Database per Service` 구현이 어렵기때문이다.

이를 해결하는것에 도움을 주는것이 Event Driven 방식이다. 이벤트 드리븐방식을통해 각 서비스에서 이벤트의 내용을 `pub/sub형식으로 전달`하고 kafka(클라우드 환경에서의 Message Channel 역할)를 통해 각 서비스에 메시지가 전달되고 처리되게 도와준다. (관련 내용은 정리의 하단부에 추가로 설명)

> 참고 블로그 포스팅 
- https://velog.io/@sa833591/%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%8B%9C%EC%8A%A4%ED%85%9C1
- https://medium.com/dtevangelist/event-driven-microservice-%EB%9E%80-54b4eaf7cc4a
- https://jaehun2841.github.io/2019/06/23/2019-06-23-event-driven-architecture/#event-stream-processing

<br>

---

#### **`MSA의 구조 및 구성효과`**

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/cicd/cna/1.jpeg)

<br>

___

#### **`API Gateway`**

<br>

`MSA의 진입점`을 통일시키는 역할을 한다. 쿠버네티스 클러스터내에서 서비스를 각각의 컨테이너로 구동시키고 Gateway 서비스에만 외부에서도 통신이 가능하게끔 `LoadBalancer` 혹은 `NodePort` 기능을 부여한다.

위 설정을 통해 일반 서비스 사용자는 오픈되어있는 Gateway로 접근 후 ***`URI Path-based`*** 로 Routing되어있는 MSA각각의 서비스( ex) 상품, 주문 배송)에 접근 할 수 있다.

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/cicd/cna/2.jpeg)

<br>

> API Gateway의 주요기능 

- 인증 및 인가 : 인증서 관리, 인증, SSL, 프로토콜 변환기능을 일괄 적용
- 요청 절차의 단순화 : 여러 클라이언트의 요청을 단일 클라이언트의 요청으로 대체가능, 클라이언트와 백엔드 간의 API 통신량을 줄여줌(대기시간 down, 효율성 up)
- 라우팅 및 로드밸런싱 : 클라이언트로부터 접수된 메시지를 API호출시 적절한 서비스로 라우팅가능, 서비스 인스턴스들에 대한 부하분산 가능
- 서비스 오케스트레이션 : 여러 개의 마이크로 서비스를 묶어 하나처럼 보이게하는 효과
- 서비스 디스커버리 : 클라우드환경(동적인환경)에서의 서비스 위치(IP주소와 포트번호)를 지원

> 통합인증에대한 방안

- Oauth 서비스 운영 : 웹, 모바일 어플리케이션에서 타사의 API 권한 획득을 위한 프로토콜
- JWT(Json Web Token)토큰 운영 : Header, Claim Set, Signature로 구성하여 사용, 요청헤더에 Authorization 값을 담아서 서버로 송신

> 참고 포스팅

- https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3API-Gateway-nvk2kf0zbj, / 
- https://medium.com/@umanking/%EC%B9%B4%ED%94%84%EC%B9%B4%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0-%ED%95%98%EA%B8%B0%EC%A0%84%EC%97%90-%EB%A8%BC%EC%A0%80-data%EC%97%90-%EB%8C%80%ED%95%B4%EC%84%9C-%EC%9D%B4%EC%95%BC%EA%B8%B0%ED%95%B4%EB%B3%B4%EC%9E%90-d2e3ca2f3c2

<br>

---
#### **`Kafka`**

<br>

> `Message Queue`

일반적인 웹서비스는 서버-클라이언트 사이의 `통신은 결합도가 높은구조이며 동기방식`으로 작동한다. (의존성이 높아 시스템에 많은영향을 주며 유연성이 낮음) 서비스간 결합도가 낮아야하는 MSA에서는 데이터의 송수신 방법으로 `비동기 방식을 사용하는것이 효율적`이다.

- **Message Queue(MQ)** 는 메시지를 발행하는 `생산자(producer, publish)`와 메시지를 받는 `소비자(consumer, subscriber)`사이에 위치하는 매개체 역할을 하는 미들웨어이다.

<br>

***`Kafka`*** 는 아파치재단의 **pub/sub 구조**에 특화된 메시지 큐이며, 많이 사용되는 이벤트 처리 방식인 분산형 데이터 스트리밍 플랫폼으로써 이벤트 스트림 게시, 구독, 저장 및 처리를 실시간으로 처리가 가능하다.

<br>

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/cicd/cna/3.jpeg)

<br>

- `pub/sub` : 클라우드 기반의 대용량 메시지 큐, 이벤트 스트림 구독 기반의 메시징 인프라이며, 이모델을 사용하면 이벤트 발생 후 또는 게시 후에 알림을 받아야 하는 구독자에게 이벤트가 전송되는 방식이다.

	MSA구조의 이벤트 파이프라인의 역할을 하는 중추적인 기능으로 Kafka가 다운되면 치명적인 장애로 이어질 수 있다.

<br>

또한, kafka는 대용량의 실시간 로그 처리에 특화된 메시징 시스템으로써 기존 범용 메시징 서비스대비 `TPS(Transaction Per Seconds)이 우수`(RabbitMQ나 ActiveMQ에 비해 높은 처리량, 및 성능을 갖고 있음)
추가 ) 프로듀서와 컨슈머를 분리해서 사용할 수 있고, 무중단으로 Scale Out(필요시 Kafka 어플리케이션 확장)이 가능하다.

> 참고 자료
- youtube : 데브원영님 강의 [Apache kafka 기본개념 및 생태계 ](https://youtu.be/catN_YhV6To)
- https://velog.io/@tedigom/MSA-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-5Backing-Service-lqk3b7560w

---

#### **`CQRS`**

CQRS(Command and Query Responsibility Segregation) : `명령과 조회의 책임 분리`를 의미하며 이는 시스템에서 명령을 처리하는 책임과 조회를 처리하는 책임을 분리하는 것이다.

- 명령 : 시스템의 상태를 변경하는 작업 / 조회 : 시스템의 상태를 반환하는 작업, 읽기전용 DB와 쓰기 DB를 분리함으로써 빠른 읽기 구현할 수 있다.

<br>
<br>

### CNA 배포, 운영 단계
---
#### **`Docker`**

<br>

> `Dokcer Engine` 

Docker는 컨테이너를 활용한 오픈소스 가상화 플랫폼이다. 컨테이너기술은 응용프로그램의 구동환경을 별도로 격리한 공간이며 하이퍼바이저를 사용한 가상화보다 단일 물리서버에 **더 많은 애플리케이션 배치가 가능하고 더 가벼우며 이식성이 뛰어나다.**

	Docker Engine(이하 Docker)가 이런 컨테이너를 구동시켜주는 역할을 한다. 어플리케이션을 서비스해야하는 서버에 개발환경과 동일한 환경(java버전, 운영체제 등)을 별도로 설치하거나 고려하지 않아도 이미지빌드가 정상이라면 `개발환경과 동일하게 구동이 가능`하다.

최초 Docker는 리눅스계열에서만 사용가능했으나 최근에는 윈도우진영에서도 docker toolbox, docker for window를 통해 VM형식으로 docker를 지원중이다.
또한 윈도우 컨테이너역시 지원한다. 

- 해당 참고 링크 : https://tech.devsisters.com/posts/intro-windows-container/

<br>

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/docker1.png)

<br>

> `Docker Image`

Docker의 이미지는 읽기 전용으로 컨테이너를 구동시킬 수 있는 `베이스 파일`의 역할을 한다. Docker 컨테이너의 default OS는 devian을 바탕으로 생성되나, 실제 커널은 **`호스트서버의 커널을 공유`** 한다. 

이는 컨테이너 내부로 접근하여 커널의 버전을 확인하는 명령어를 입력했을 시 쉽게 확인이 가능. 따라서 시스템상의 구현체는 devian os가 탑재되어있지만, 호스트의 커널을 사용하기 때문에 별도로 OS를 띄운 `VM보다 훨씬더 가볍고 빠르다.`

이미지는 앞서 언급한것처럼 읽기전용이기때문에 컨테이너가 구동중인상태에서 어플리케이션의 변화(로그데이터, 운영데이터 등) 변경은 컨테이너내에 쌓인다.
**즉, 컨테이너가 삭제되면 어플리케이션을 운영하며 생긴 데이터들이 유실**되므로 도커가 제공하는 Volume기능을 이용하여 `호스트 공간에 마운트할 필요`가 있다.

	Docker image는 `Dockerfile`이라는 파일을 인식하고 해당 파일의 내용을 인식하여 단계별로 빌드한다.

<br>

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/logo.png)

<br>

> `Docker Repository`

Docker image를 빌드 이후 서버에서 기동시키위해서는 `docker repository`에 docker image를 업로드해야한다. (운영서버에서 이미지를 다운받아서 사용하기 위함)

	default로 사용되는 docker repository는 dokcer hub(https://hub.docker.com/)이지만 기업이나 정부기관에서 사용할 목적의 private한 서비스를 위해 docker hub private이나 Amazone ECR과 같은 플랫폼이 존재한다

<br>

---

#### **`Kubernetes`**

<br>

> `오케스트레이션(Orchestration)`


도커는 그자체로도 매우 강력한 장점이 있지만, 운영시스템에서 단독으로 사용하기에는 제약사항이 많다.

특히, 도커 Volume 같은 기능자체도 최근에 생긴 기능이기때문에 도커 `컨테이너들의 생명주기를 관리해주고 전체적인 통신도 관장`해줘야하는 오케스트레이터가 필요하다.

이런 이유로 탄생한것이 쿠버네티스이며 **`k8s`** 라고 줄여서 지칭한다.

<br>

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/concept/1.png)

<br>

> `master node`

쿠버네티스는 `master node` 한 대와 `worker node` 여러개로 구성되어있다. 이런 노드들이 묶여 하나처럼 보이게 하는 것을 쿠버네티스 클러스터라고 한다.

클러스터안의 `Namespace`라는것이 쿠버네티스의 오브젝트들을 독립된 공간으로 만들수 있게 도와준다. 

<br>

> `쿠버네티스의 오브젝트`
 
쿠버네티스는 `pod, service, controller`등 다양한 오브젝트들을 가진다. 

중요한 오브젝트들을 정리하자면 **pod**에 docker repository에 업로드된 이미지를 사용하여 컨테이너를 구동시키고 **service**로 해당 pod를 외부에 오픈하며, **controller**로 해당 파드들에 대한 생명주기를 관리한다.

<br>

> `쿠버네티스의 볼륨`

앞서 언급했던것 처럼 컨테이너내부의 데이터는 휘발성 데이터이기때문에 호스트서버 어딘가에 마운트해서 사용해야할 필요가 있다.

쿠버네티스는 **emptyDir, Hostpath**등의 기능을 지원하지만 가장 많이 사용하는것은 `PVC/PV` 방식이다.

`PVC/PV`는 일반 사용자가 구동시킬 컨테이너 서비스가 필요한 용량에대해 Persistent Volume Claim(PVC)를 요청하면 관리자가 정적 혹은 동적으로 PV를 할당해주는 방식이다.

---

