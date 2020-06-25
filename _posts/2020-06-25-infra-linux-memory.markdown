---
layout: post
title: Linux 메모리 사용량 확인 및 관리 
subtitle: [CentOS]Linux 메모리 사용량 확인 및 관리
categories: infra
tags: infra linux
comments: true
published: true
header-img: img/infra/linux/memory.jpg
---

## 개요
---
> Linux 메모리 사용량 확인 및 관리


### 리눅스 메모리 사용량 확인
---

리눅스 쉘에 아래와 같은 명령어를 입력한다.

> # free

![그림1](https://zunoxi.github.io/assets/linux/memory/free.png)

- 용어 설명
    - total : 서버에 할당된 전체 물리적 메모리의 크기
    - used : 실제 사용중인 메모리의 크기 
    - free : 사용중이 아닌 메모리 크기
    - shared : 공유 메모리 크기
    - buff/cache : 버퍼/캐시 메모리로 사용되고 있는 메모리 크기
    - available : 실질적으로 사용가능한 메모리 크기(swapping 없이 할당 가능한 크기)

> [`Swapping?`]




### TLS
---

TLS(Transport Layer Security)는 1999년 새버전의 SSL로서 도입되었으며(IETF에서 개발) SSL 3.0 버전을 기반으로 했다.

현재는 TLS 1.0, 1.1, 1.2, 1.3(최신)으로 총 4개의 버전이 존재한다.




### 인증서와 프로토콜
---

> 인증서와 프로토콜은 동일하지 않다.

우리는 인증서가 프로토콜에 의존하지 않는다는 점을 알아야한다.

프로토콜은 인증서 자체가 아니라 서버구성에 따라 결정되는것이므로 "SSL 및 TLS와 함께 사용할 인증서" 라고 하는것이 정확함.

많은 사람들이 익숙한 용어이기 떄문에 SSL 인증서라고 부르지만 SSL/TLS 라는 용어 자체가

사람들이 TLS에 익숙해질 때까지 공통적인 절충안이다.


### SSL과 TLS

---

서로 다른 암호를 사용한다고 할 수 있다.

SSL2와 SSL3는 상호운용되지 않으며, SSL3과 TLS1도 서로호환되지 않는다.

서버 구성 측면에서 볼때 취약점, 구식 암호 제품군 및 브라우저 보안경고의 차이가 있는 프로토콜이라고 할 수 있다.



### TLS 1.0, TLS 1.1 버전의 지원중단

---

> Chrome, IE, Edge등 2020년부터 TLS 1.0, TLS 1.1 암호화 프로토콜 통신 지원 중단예정

코로나의 여파로 2020년 상반기지원중지의 상황에서 현재 일정변경중인 상태이다.

구버전인 TLS 1.0, 1,1은 Poodle과 Beast 같은 공격에 취약하다.


---

> [`summary`]

- SSL은 웹 네트워크에서 사용되는 인터넷 암호화 프로토콜이며 TLS의 전신이다.
- SSL 버전 중 현재 사용가능한것은 없고 TLS 를 사용해야한다.
- 또한 TLS 역시 1.0과 1.1 버전은 곧 지원이 중단될 예정이다.
- 우리가 일반적으로 표현하는 SSL 인증서는 잘못된 표현이고 사실상 `TLS를 사용하는 서버에 적용될 인증서` 라고 표현하는것이 맞다.
