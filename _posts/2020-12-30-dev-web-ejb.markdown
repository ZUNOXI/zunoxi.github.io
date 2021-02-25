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
	- [`Java Bean이란`](#java-bean이란)
	- [`EJB의 구조`](#ejb의-구조)
	- [`EJB의 장단점`](#ejb의-장단점)
  
## EJB
---
최근 Spring 프레임워크가 EJB를 대체하기위한 대안으로 개발되며 EJB는 이전만큼 많이 쓰이지는 않는것같다. 그러나 큰 기업이나 기관의 메인서비스는 여러사정으로 여전히 EJB를 사용하는곳이 있다.(필자도 그런환경이라 어쩔수 없이 배우고있..) 구글링을 통해 EJB에 대한 개념과 그 아키텍쳐를 정리함으로 EJB에 대해 이해해보려한다.

<br>

**참고했던 블로그 포스트**

-   [https://woongsin94.tistory.com/357](https://woongsin94.tistory.com/357)
-   [https://mangkyu.tistory.com/14](https://mangkyu.tistory.com/14)
-   [https://elfinlas.github.io/2017/10/31/javabean/](https://elfinlas.github.io/2017/10/31/javabean/)
-   [https://spidyweb.tistory.com/27](https://spidyweb.tistory.com/27)

<br>

---
### **Java Bean이란?**

EJB는 `엔터프라이즈 자비빈즈(Enterprise JavaBeans)`의 약자로 EJB에 대한 이해이전에 자바빈즈(Java Beans)에 대해 먼저 이해하고 넘어가는것이 좋을 것 같다. 

자바빈즈는 자바로 작성된 소프트웨어 컴포넌트들을 지칭한다. 자바는 프로그램 기본단위가 클래스이고, **`자바빈은 그 클래스들이 복합적으로 이루어진 구조`** 를 말한다.

<br>

EJB와 용어가 비슷해보이고 Sun에서 만든것은 같으나 자바빈즈와 EJB는 그 만들어진 `목적이 다르고 주로 동작하는 위치도 다르다`. 자바빈즈는 비주얼 개발환경에서 사용되는 재사용가능한 컴포넌트이며, EJB는 서버쪽 비지니스 어플리케이션에 사용되는 분산객체 컴포넌트 모델이라는것에 목적이 다름을 알 수 있다. 

또한, EJB는 오직 서버에서만 동작하지만 자바빈즈는 클라이언트에서 서버로 통신하는 경로에서 사용된다.

<br>

---
### **EJB의 구조**

본격적으로 EJB는 _**거대규모 시스템 구축을위한 컴포넌트 모델**_ 이라고 설명할 수 있다. 여기서 컴포넌트 모델이라는것은 대략 각각의 소프트웨어를 독립적인 모듈로 제작하여 **`재사용성과 호환성을 높이는 개념`** 이다.

일반적으로 사용되는 Java EE의 API로 클라이언트가 볼 수 있는 **화면단로직은 JSP**가, **비지니스로직은 EJB**가 구현하는 구조로 구성되어있다. 또한, 비지니스 로직을 구현한것을 `Enterprise Bean`이라고 칭하며 Database처리, Transaction처리와 같은 시스템 서비스를 구현한 부분을 `컨테이너`라고 부른다.

<br>

---

### **(1) Enterprise Bean**

Enterprise Bean은 비지니스 로직을 실행하는 서버 컴포넌드인데, 보통 2가지의 모델을 갖고있다.

- `세션 빈(Session Bean)` : DB연동이 없이 구동가능하다. 주로 로직이 위치한다.
- `엔티티 빈(Entity Bean)` : 데이터베이스의 데티러 I/O 전반을 관리하는 객체
  - Insert, Update, Delete, Select를 관리
  - 클라이언트가 Session Bean을 호출하고 Session Bean이 Entity Bean을 호출하며 데이터 베이스에 접근하는 구조이다.


<br>



### **(2) Container**

EJB 컨테이너는 Application Server내에서 _**Enterprise Bean에 대한 런타임 환경을 제공**_ 한다. 일반적으로 `EJB서버와 EnterpriseBean 사이에 통신`을 하게 해주는 역할을 한다고 이해할 수 있다. Sevlet이 Apache Tomcat같은 Servlet Container에 올려서 서비스 되는것 처럼, EJB는 Weblogic, JBoss와 같은 EJB Container에 올려서 서비스가 된다. 

보통의 경우, 사용자는 클라이언트 어플리케이션을 사용하고 이는 컨테이너를 경유해서 Enterprise Bean에 접근하는것으로 이해할 수 있다.

EJB 컨테이너는 다음과 같이 엔터프라이즈 Bean에 많은 서비스를 제공한다.

- 필요 시 트랜잭션 시작, commit or rollback
- 수신 요청에 대해 엔터프라이즈 Bean 인스턴스 풀을 준비상태로 유지보수 및 비활성 풀과 활성 상태 간 인스턴스 이동, Bean내의 스레드 조건 충족 보장
- 엔티티 Bean의 인스턴스 변수와 지속 스토리지에 저장된 해당 데이터 항목의 자동 동기화

<br>


### **(3) EJB Server**

EJB Server는 컨테이너를 관리해서 EJB로서 필요한 시스템 서비스(데이터베이스 처리, 트랜젝션 처리 등)를 구현한다.

<br>

### **(4) Client application**

EJB구조를 사용함에 있어 이와 연결할 수 있는 클라이언트단의 어플리케이션이다. Java Applet, Java Application, Servlet, JavaServer Pages(JSP)가 그 종류이다.

<br>

---

### **EJB의 장단점**

<br>

> 장점

- 객체를 미리 생성하여 메모리에 저장해 사용준비가 된 후 서버가 동작
  
  -> 많은 동시접속자에 대해 안정성을 지원함(인스턴스 풀링)
- 자동으로 컨테이너가 모든 처리메소드에 대하여 트랜잭션을 처리해준다.
- 빈즈의 상태를 메모리에서 사용여부에 따라 자동으로 활성화/비활성화를 실행하여 관리해준다.
- 분산기능을 지원한다.

<br>

> 단점

- 가장 큰 문제로 지적되는것이 `실행 속도가 느리다`는것이다. 이는 분산환경을 지원하기 위해 객체를 직렬화하는 과정 때문에 실행 속도의 저하가 발생한다.
- 복잡한 프로그래밍 모델
- 특정환경, 특정기술에 종속적인 코드
- 자동화된 테스트가 매우 어렵거나 불가능

<br>

---

Spring Framework 등작이후 자바진영에서의 EJB는 잠깐 유행했던 기술정도로 여겨지는것 같다. 다만, 일부 공공기관 대기업MES에는 여전히 쓰이고 있기때문에 관련 업계에 있는한 알아두면 좋을것 같다.