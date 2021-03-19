---
layout: post
title: Web 1. Spring Framework와 Spring-boot 차이
subtitle: Spring Framework와 Spring-boot 차이
categories: programming
tags: programming web
comments: true
published: true
header-img: img/dev/web/spring-jar/1.png
---

## 개요
> `스프링`과 `스프링부트(spring-boot)`에 대한 이해
  
- 목차
	- [`Spring의 몇 가지 용어`](#spring과-spring-boot의-차이)
	- [`Spring의 주요특징`](#spring의-몇-가지-용어)
	- [`Spring Boot와의 차이`](#spring의-주요특징)
  
## Spring Framework
---
스프링 프레임워크는 `자바 생태계`에서 가장 대중적인 개발 프레임워크이다. 한국에서는 정부에서 공공기관의 웹 개발 시 기반으로 사용되는 _**전자정부 프레임워크**_ 로 불리고 있다.


<br>


---

### **Spring의 몇 가지 용어**

<br>

-   Bean : 자바 객체이다. 스프링 빈은 스프링 컨테이너에서 만들어지는 자바 객체를 스프링 빈이라고 부른다.

-   Spring Container : 주입을 이용하여 객체를 관리하는 컨테이너. 빈의 생성과 관계, 사용, 생명 주기 등을 관장.  
      
    객체 간의 의존성을 낮추기 위해 사용  
      
    
-   Bean Factory : 스프링 컨테이너의 종류로 빈(Bean) 객체를 생성하고 관리하는 클래스(팩토리 패턴 구현)  
      
    
-   Application Context : 빈 팩토리와 유사하지만 향상된 형태의 컨테이너, 빈 팩토리와의 차이점은 ApplicationContext   
      
    
-   POJO(Plain Old Java Object) : 어플리케이션의 핵심 코드를 담고 있는 클래스. 특정 환경에 종속 없는 단순 클래스이다. 

---

### **Spring의 주요특징**

<br>

-   제어의 역전(Inversion of Control) : 전체적인 `개발의 흐름을 프레임워크에 전적으로 위임`하고 개발자는
    그 흐름 안에 들어가서 개발을 하게 되는 것. 쉽게 설명하면 어떠한 일을 하도록 만들어진 프레임워크(Spring)에 제어의 권한을 넘김으로써 개발자가 코드 작성에 신경 써야 할 것을 줄이는 방법이다.  
      
    > 개발의 주체가 사용자에게서 프레임워크로 넘어갔기 때문에 제어의 역전이라고 부른다. 위의 방식을 사용하여 각 `모듈 간의 의존성을 낮추는 효과`를 준다.  
      
    
-   의존성 주입(Dependency Injection) : 개발에 필요한 라이브러리, 모듈들을 pom.xml에 선언함으로써    프레임워크가 자동으로 `Maven or Gradle을 통해 다운로드`하고 의존성을 서로 연결시켜 주는 역할을 한다.  
      
    
-   관점 지향 프로그래밍(Aspect Oriented Programming) 

---

### **Spring과 Spring Boot의 차이**

<br>

`스프링` 자체가 지원해주는 것이 많은 만큼 `복잡하다`. 스프링 부트를 사용하면 '바로 실행' 할 수 있는 스탠드 얼론, 프로덕션급 스프링 기반 애플리케이션을 쉽게 만들 수 있다. (스프링의 프로젝트 중 하나)

> `스프링부트`를 사용하면 스프링 프레임워크 초기 사용 설정에 많은 부분을 `자동화시켜주고 편리하게 사용`할 수 있게 해 준다. 또한, 톰캣을 내장하고 있기 때문에 웹 애플리케이션 서버를 상대적으로 쉽게 만들 수 있다. 

또한 일반적인 **스프링** 같은 경우는 웹서비스 제공시 `외장 WAS`가 필요하나, **스프링부트**는 `내장WAS`를 포함하고 있기때문에 이역시 간편함에서 차이가 발생한다.

결국, 이 둘의 차이는 최초 설정의 간편함의 차이라고 할 수 있으며, 일반적으로 간단한 웹 어플리케이션 서비스를 제공한다면 스프링 부트가 효율적이라고 할 수 있다. 다만, 대규모 서비스를 제공하는 경우는 Spring(legacy)를 채택하고 오픈소스 `아파치톰캣`이나 오라클의 `Weblogic`, Tmax의 `JEUS` 같은 별도의 외장 WAS를 사용하는 것이 더 좋을 것이다.