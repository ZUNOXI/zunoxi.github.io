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

해당 포스팅은 [https://zunoxi.tistory.com/66?category=950191](https://zunoxi.tistory.com/66?category=950191)으로 이전되었습니다.

<!--

## 개요
> 쿠버네티스의 `ConfigMap, Secret`에 대한이해
  
- 목차
	- [`ConfigMap`](#configmap)
	- [`Hostpath`](#secret)
  
## ConfigMap and Secret
---
`사소한 설정값` 몇개 때문에, 큰 용량의 이미지를 별도로 관리한다는것은 매우 비효율적인일이다. 따라서 이런 간단한 정보는 컨테이너 `내부에서 외부 정보를 참조`할 수 있게 해주는 기능이있다. 그것이 바로 ***ConfigMap과 Secret***이다.

<br>

### ConfigMap

---
우리는 종종 개발환경과 상용환경이 달라서 생기는 몇가지 문제들을 직면한다. 예를들어, `SSH설정, key값등에 대한 차이`로 개발계에서 테스트하는 이미지와 운영계에서 운영할 이미지를 별도로 만들어야될 수 있겠다. 아래사진과 같이 말이다.

<br>

![그림1](/assets/img/devops/k8s/configmap/1.jpeg)


여기서 ***ConfigMap***을 사용하면 이미지가 구동되는 컨테이너 전체의 설정을 바꾸는 것이 아니라, ConfigMap의 설정만 바꾸고 컨테이너에서는 해당 설정값들을 ConfigMap에 있는 값들을 참조하게 설정할 수 있다. 즉, `같은 이미지파일로` 개발계와 운영계의 컨테이너를 구동시킬 수 있다. 

<br>

![그림2](/assets/img/devops/k8s/configmap/2.jpeg)


> 위 내용처럼 `Secret`도 동일한 원리로 사용된다.
> 
***ConfigMap***은 `키(key)와 상수(Value)`로 구성되어있다. 일반적으로 컨테이너내의 환경변수에 세팅을 할 수 있으며 기밀이 아닌 데이터를 저장하는데 사용하는 API오브젝트이다. 파드는 볼륨에서 **환경변수, 커멘드-라인 인수 또는 구성파일**의 정보를 ConfigMap을 통해 참조할 수 있다.

<br>

### Secret

---

***Secret***도 `키(key)와 상수(Value)`로 구성되어있다. Secret은 보안과 관련된 값을 저장하는 기능을한다. 패스워드, OAuth 토큰, ssh키와 같은 민감한 정보를 저장하고 관리할때 사용할 수 있으며, 데이터 삽입시 `Base64 인코딩`을 해서 넣어야하는 것이 ConfigMap과의 차이이다. 

> 단, pod로 주입이 될때는 자동으로 디코딩이 되어서 대시보드환경에서는 다 보여지게된다.

또한, Secret은 보통 메모리에 저장이된다. 메모리에 저장되는것이 보안에는 더 좋지만 Secret을 너무 많이 만들면 메모리 성능에 영향으 줄 수 있다는점도 주의해야한다.

<br>

### Env

___

또하나 참고할점으로 ConfigMap, Secret을 환경변수(Env)로 사용할 때는 총 3가지의 방법이 있다. 이는 `Literal, File, Volume Mount`방식이며 다음과 같다.

- `Env(Literal)` : 상수를 넣어서 ConfigMap, Secret을 구상하는 방법.
- `Env(file)`
  - 파일이름이 key, 내용이 value인 경우
  - 한번주입되면 끝이다. ConfigMap파일의 내용이 변경되어도 pod에 영향을 주지 않는다.
  - pod가 삭제되고 재생성될때 영향을 받는다.
  - secret의 파일 내용에 명령어로 base64인코딩이 되어있는경우 2번 인코딩 될 수 있으니 주의해야한다.
- `Env(Volume Mount)`
  - 컨테이너안에 호스트 볼륨을 마운트한다.
  - 마운트된 볼륨의 내용을 변경하면 컨테이너안의 Env도 함께 동기화 되는것이 Env(file)과의 차이점이다.

<br>

---
> 참고
> - [`쿠버네티스 공식문서`](https://kubernetes.io/ko/docs/concepts/configuration/secret/)
> - [`김태민님의 쿠버네티스 강의`](https://www.inflearn.com/course/%EC%BF%A0%EB%B2%84%EB%84%A4%ED%8B%B0%EC%8A%A4-%EA%B8%B0%EC%B4%88#)

-->
