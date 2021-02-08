---
layout: post
title: Web 10. EJB에대한 이해
subtitle: EJB와 Spring의 차이를 이해한다.
categories: programming
tags: programming web
comments: true
published: true
header-img: img/dev/web/ejb/ejb.jpg
---

## 개요
> 자바의 서버모델중 하나인 EJB에 대한 정리.
  
- 목차
	- [`Github`](#2-github)
	- [`Gitlab`](#3-gitlab)
	- [`Bitbucket`](#4-bitbucket비트버킷)
	- [`비교정리`](#5-정리)
  
## EJB
---
최근 Spring 프레임워크가 EJB를 대체하기위한 대안으로 개발되며 EJB는 이전만큼 많이 쓰이지는 않는것같다. 그러나 큰 기업이나 기관의 메인서비스는 여러사정으로 여전히 EJB를 사용하는곳이 있다. 관련하여 EJB에 대한 개념과 그 아키텍쳐를 정리함으로 EJB에 대해 이해해보려한다. 

<br>

**참고했던 블로그 포스트**

-   [https://woongsin94.tistory.com/357](https://woongsin94.tistory.com/357)
-   [https://mangkyu.tistory.com/14](https://mangkyu.tistory.com/14)


<br><br>

---
### **1. EJB란?**

<br>

EJB는 **엔터프라이즈 자비빈즈**(Enterprise JavaBeans)의 약자로 기업환경같은 거대규모 시스템 구축을위한 `컴포넌트 모델`이다. 여기서 컴포넌트 모델이라는것은 [링크](https://mommoo.tistory.com/55)를 참고하길바라며, 대략 각각의 소프트웨어를 독립적인 모듈로 제작하여 재사용성과 호환성을 높이는 개념으로 이해할 수 있다.

일반적으로 사용되는 Java EE의 API로 클라이언트가 볼 수 있는 화면단로직은 JSP가, 비지니스로직은 EJB가 구현하는 구조로 구성되어있다. 또한, 비지니스 로직을 구현한것을 `Enterprise Bean`이라고 칭하며 Database처리, Transaction처리와 같은 시스템 서비스를 구현한 부분을 `컨테이너`라고 부른다.


<br>

---
#### **(1) Enterprise Bean**

Enterprise Bean은 비지니스 로직을 실행하는 서버 컴포넌드인데, 보통 2가지의 모델을 갖고있다.

- 세션 빈(Session Bean) : DB연동이 없이 구동가능하다.
- 엔티티 빈(Entity Bean) : 데이터베이스의 데티러 I/O 전반을 관리하는 객체
  - Insert, Update, Delete, Select를 관리