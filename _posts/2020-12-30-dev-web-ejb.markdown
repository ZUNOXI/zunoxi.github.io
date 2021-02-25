---
layout: post
title: Web 10. ejb에대한 이해
subtitle: ejb에 대해 이해하기
categories: programming
tags: programming web
comments: true
published: true
header-img: img\dev\web\ejb\ejb.jpg
---

## 개요
> 자바의 서버모델중 하나인 EJB에 대한 정리.
  
- 목차
	- [`Spring boot`](#1-spring-boot)
	- [`jar format`](#2-jar-format)
	- [`pom.xml 설정`](#3-pomxml-설정)
	- [`jar build`](#4-jar-build)
  
## EJB
---
최근 Spring 프레임워크가 EJB를 대체하기위한 대안으로 개발되며 EJB는 이전만큼 많이 쓰이지는 않는것같다. 그러나 큰 기업이나 기관의 메인서비스는 여러사정으로 여전히 EJB를 사용하는곳이 있다.(필자도 그런환경이라 어쩔수 없이 배우고있..) 관련하여 EJB에 대한 개념과 그 아키텍쳐를 정리함으로 EJB에 대해 이해해보려한다.

<br>

**참고했던 블로그 포스트**

-   [https://woongsin94.tistory.com/357](https://woongsin94.tistory.com/357)
-   [https://mangkyu.tistory.com/14](https://mangkyu.tistory.com/14)

<br>
---

### **EJB란?**

EJB는 `엔터프라이즈 자비빈즈(Enterprise JavaBeans)`의 약자로 기업환경같은 _**거대규모 시스템 구축을위한 컴포넌트 모델**_ 이다. 여기서 컴포넌트 모델이라는것은 대략 각각의 소프트웨어를 독립적인 모듈로 제작하여 **`재사용성과 호환성을 높이는 개념`** 으로 이해할 수 있다.

일반적으로 사용되는 Java EE의 API로 클라이언트가 볼 수 있는 **화면단로직은 JSP**가, **비지니스로직은 EJB**가 구현하는 구조로 구성되어있다. 또한, 비지니스 로직을 구현한것을 Enterprise Bean이라고 칭하며 Database처리, Transaction처리와 같은 시스템 서비스를 구현한 부분을 컨테이너라고 부른다.

<br>

---

### **(1) Enterprise Bean**

Enterprise Bean은 비지니스 로직을 실행하는 서버 컴포넌드인데, 보통 2가지의 모델을 갖고있다.

- `세션 빈(Session Bean)` : DB연동이 없이 구동가능하다. 주로 로직이 위치한다.
- `엔티티 빈(Entity Bean)` : 데이터베이스의 데티러 I/O 전반을 관리하는 객체
  - Insert, Update, Delete, Select를 관리
  - 클라이언트가 Session Bean을 호출하고 Session Bean이 Entity Bean을 호출하며 데이터 베이스에 접근하는 구조이다.


<br>

---

### **(2) Container**

EJB 컨테이너는 Application Server내에서 _**Enterprise Bean에 대한 런타임 환경을 제공**_ 한다. 일반적으로 `EJB서버와 EnterpriseBean 사이에 통신`을 하게 해주는 역할을 한다고 이해할 수 있다. Sevlet이 Apache Tomcat같은 Servlet Container에 올려서 서비스 되는것 처럼, EJB는 Weblogic, JBoss와 같은 EJB Container에 올려서 서비스가 된다. 

보통의 경우, 사용자는 클라이언트 어플리케이션을 사용하고 이는 컨테이너를 경유해서 Enterprise Bean에 접근하는것으로 이해할 수 있다.

EJB 컨테이너는 다음과 같이 엔터프라이즈 Bean에 많은 서비스를 제공한다.

- 필요 시 트랜잭션 시작, commit or rollback
- 수신 요청에 대해 엔터프라이즈 Bean 인스턴스 풀을 준비상태로 유지보수 및 비활성 풀과 활성 상태 간 인스턴스 이동, Bean내의 스레드 조건 충족 보장
- 엔티티 Bean의 인스턴스 변수와 지속 스토리지에 저장된 해당 데이터 항목의 자동 동기화

<br>

---

### **(3) EJB Server**

