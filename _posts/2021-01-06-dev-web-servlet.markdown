---
layout: post
title: Web 11. Servlet과 JSP
subtitle: servlet과 jsp에 대한 이해
categories: programming
tags: programming web
comments: true
published: true
header-img: img\dev\web\jsp\1.png
---

## 개요
> Servlet과 JSP에 대한 정리
  
- 목차
	- [`Servlet이란?`](#servlet이란)
	- [`JSP이란`](#jsp이란)
	- [`Servlet과 JSP 역할`](#servlet과-jsp-역할)
  
## Servlet과 JSP
---
서버사이드 개발 시에는 Servlet과 JSP에 대한 이해는 필수로 여겨진다. 필자역시 SE이지만 가끔 백 엔드 개발을할때도 있는데, 개념을 명확하게 정리하지 않아서 구조 파악에 애를 먹은 적이애를먹은적이 꽤 있다. 따라서 헷갈림방지(?)를 위해 용어 및 쓰임새를 정리하고자 한다.


<br>

**참고했던 블로그 포스트**

-   [https://m.blog.naver.com/acornedu/221128616501](https://m.blog.naver.com/acornedu/221128616501)
-   [https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%84%9C%EB%B8%94%EB%A6%BF](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EC%84%9C%EB%B8%94%EB%A6%BF)

<br>

---
### **Servlet이란?**

자바 서블릿(Java Servlet)은 자바를 사용하여 `웹페이지를 동적으로 생성하는 서버측 프로그램을 의미`한다. 

<br>

![그림1](/assets/img/dev\web\jsp\2.png)

<br>

정확하게는 서블릿은 Java코드 안에 HTML태그가 삽입되며 자바언어로 되어있는 .java가 그 확장자이다. 서블릿은 자바언어를 웹 어플리케이션에 조금 더 개발하기 쉽게 하기 위해 만든 API이며 이 규약에 맞는 라이브러리나 클래스들을 상속 및 구현하여 만든 클래스들을 서블릿이라고 한다. Java코드안에 HTML코드가 들어있는 하나의 클래스라고 이해할 수 있다. 

프로그래밍 구조를 설계할때 Controller(data processing)으로 사용이 용이하며, DB와의 통신과 비지니스로직 호출에 유리하다.



<br>

---

### **JSP이란?**

JSP(Java Server Pages)는 서블릿과 비슷한 점이 있지만, **`JSP가 HTML 문서안에 Java코드를 포함하고 있는 반면 서블릿은 자바코드 안에 HTML을 포함`** 하고 있다는 차이점이 있다.

<br>

![그림2](/assets/img/dev\web\jsp\3.png)

<br>

이는 서블릿 기반의 스크립트 기술이며 기존 서블릿 이용시 자바에 대한 지식이 필요하다는 단점이 있는데, JSP를 사용하면 서블릿을 작성하지 않고도 간편하게 웹프로그래밍을 가능하게 할 수 있다. 즉, `키워드가 태그화` 되어 서블릿에 비해 배우기 쉽다는 것이다.

또한, JSP는 Presentation(View)에 좋고 JSP수정이후는 WAS의 재기동없이 바로 반영되기때문에 배포가 쉽다.


<br>

---
### **Servlet과 JSP 역할**

일반적으로 `JSP`는 웹 어플리케이션 구조에서 사용자에게 결과를 보여주는 `프리젠테이션층`을 담당하고 `Servlet`은 사용자의 요청을 받아 분석하고 비지니스층과 통신하여 처리한 결과를 다시 사용자에게 응받하는 `컨트롤러층`을 담당한다.


JSP의 기술 발표로 인해 기존 `Model1방식에서 Model2(일명 MVC패턴) 방식으로 개발트렌드가 변경`되었는데 관련내용은 다음 포스팅에서 진행해보려한다.

