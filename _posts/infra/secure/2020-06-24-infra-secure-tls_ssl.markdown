---
layout: post
title: Secure 1. TLS와 SSL에 대한 이해
subtitle: TLS와 SSL에 대한 정리
categories: infra
tags: infra secure
comments: true
published: true
header-img: img/infra/secure/tlsssl/tlsssl.jpg
---

해당 포스팅은 [https://zunoxi.tistory.com/104?category=950186](https://zunoxi.tistory.com/104?category=950186)으로 이전되었습니다.

<!--

## 개요
> `TLS`와 `SSL`에 대한 정의와 그차이
  
- 목차
	- [`SSL이란?`](#SSL)
	- [`TLS이란?`](#TLS)
	- [`SSL과 TLS`](#ssl과-tls)
	- [`인증서와 프로토콜`](#인증서와-프로토콜)
  
## SSL
---
먼저 `SSL(Secure Socket Layers)`은 Netscape에 의해 개발되었으며 네트워크를 통해 작동하는 서버, 시스템 및 응용프로그램간에 인증 및 데이터 암호화를 제공하는 인터넷 암호화 프로토콜이다.

<br>

현재 SSL 2.0및 3.0 모두 IETF에 의해 사용이 중지되었다. 이러한 이유로 서버 구성시 SSL 2.0 및 3.0을 비활성해야하며 TLS 프로토콜만 사용하도록 설정해야한다.

<br>

### TLS
---

`TLS(Transport Layer Security)`는 1999년 새버전의 SSL로서 도입되었으며(IETF에서 개발) SSL 3.0 버전을 기반으로 했다.

현재는 TLS 1.0, 1.1, 1.2, 1.3(최신)으로 총 4개의 버전이 존재한다.

<br>

### SSL과 TLS

---

서로 다른 암호를 사용한는 프로토콜이라고 할 수 있다.

SSL2와 SSL3는 상호운용되지 않으며, SSL3과 TLS1도 서로호환되지 않는다.

서버 구성 측면에서 볼때 취약점, 구식 암호 제품군 및 브라우저 보안경고의 차이가 있는 프로토콜이라고 할 수 있다.

<br>

### 인증서와 프로토콜
---

> 인증서와 프로토콜은 동일하지 않다.

우리는 인증서가 **`프로토콜에 의존하지 않는다`** 는 점을 알아야한다.

프로토콜은 인증서 자체가 아니라 서버구성에 따라 결정되는것이므로, 일반적으로 우리가 부르는 `SSL인증서`라는 용어 보다는 `"SSL 및 TLS와 함께 사용할 인증서"` 라고 하는것이 정확하다.

또한, 많은 사람들이 익숙한 용어이기 떄문에 SSL 인증서라고 부르지만 SSL/TLS 라는 용어 자체는 사람들이 TLS에 익숙해질 때까지 공통적인 절충안이라는점을 기억해야한다.

<br>

### TLS 1.0, TLS 1.1 버전의 지원중단

---

> Chrome, IE, Edge등 2020년부터 TLS 1.0, TLS 1.1 암호화 프로토콜 통신 지원 중단예정

코로나의 여파로 2020년 상반기지원중지의 상황에서 현재 일정변경중인 상태이다.

구버전인 TLS 1.0, 1,1은 Poodle과 Beast 같은 공격에 취약하다.


---

> `summary`

- SSL은 웹 네트워크에서 사용되는 인터넷 암호화 프로토콜이며 TLS의 전신이다.
- SSL 버전 중 현재 사용가능한것은 없고 TLS 를 사용해야한다.
- 또한 TLS 역시 1.0과 1.1 버전은 곧 지원이 중단될 예정이다.
- 우리가 일반적으로 표현하는 SSL 인증서는 잘못된 표현이고 사실상 `TLS를 사용하는 서버에 적용될 인증서` 라고 표현하는것이 맞다.

<br>

본 포스팅은 `링크의 포스팅`(https://smartits.tistory.com/209)을 읽고 정리함.

-->
