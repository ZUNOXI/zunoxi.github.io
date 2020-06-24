---
layout: post
title: TLS와 SSL에 대한 이해
subtitle: TLS와 SSL에 대한 정리
categories: infra
tags: infra secure
comments: true
published: true
header-img: img/infra/secure/tlsssl/tlsssl.jpg
---

## 개요
---
> TLS와 SSL에 대한 정의와 그차이

-   목차
    - [`Java 프로젝트에서의 RESTapi`](#Java-프로젝트에서의-RESTapi)
    - [`구조`](#구조)
    - [`Sample 예제`](#Sample-예제)

> [`해당 포스팅`](https://smartits.tistory.com/209)을 읽고 정리함.


### SSL
---

먼저 SSL(Secure Socket Layers)은 Netscape에 의해 개발되었으며

네트워크를 통해 작동하는 서버, 시스템 및 응용프로그램간에 인증 및 데이터 암호화를 제공하는 인터넷 암호화 프로토콜이다.




### TLS
---

TLS(Transport Layer Security)는 1999년 새버전의 SSL로서 도입되었으며 SSL 3.0 버전을 기반으로 했다.

현재 SSL 2.0및 3.0 모두 IETF에 의해 사용이 중지되었다.

이러한 이유로 서버 구성시 SSL 2.0 및 3.0을 비활성해야하며 TLS 프로토콜만 사용하도록 설정해야한다.



### 인증서와 프로토콜
---

> 인증서와 프로토콜은 동일하지 않다.

우리는 인증서가 프로토콜에 의존하지 않는다는 점을 알아야한다.

프로토콜은 인증서 자체가 아니라 서버구성에 따라 결정되는것이므로 "SSL 및 TLS와 함꼐 사용할 인증서" 라고 하는것이 정확함.

많은 사람들이 익숙한 용어이기 떄문에 SSL 인증서라고 부르지만 SSL/TLS 라는 용어 자체가

사람들이 TLS에 익숙해질 때까지 공통적인 절충안이다.


### SSL과 TLS

서로 다른 암호를 사용한다고 할 수 있다.

SSL2와 SSL3는 상호운용되지 않으며, SSL3과 TLS1도 서로호환되지 않는다.

정리하자면 서버 구성 측면에서 볼때 취약점, 구식 암호 제품군 및 브라우저 보안경고의 차이가 있는 프로토콜이라고 할 수 있다.

