---
layout: post
title: Web 12. Mode1패턴과 Model2패턴
subtitle: Model1과 Model2
categories: programming
tags: programming web
comments: true
published: true
header-img: img\dev\web\jsp\1.png
---

## 개요
> Mode1패턴과 Model2패턴에 대한 정리
  
- 목차
	- [`Servlet이란?`](#servlet이란)
	- [`JSP이란`](#jsp이란)
	- [`Servlet과 JSP 역할`](#servlet과-jsp-역할)
  
## Model1 and Model2
---
바로 전 포스팅에서 서블릿과 JSP에 대해 알아봤다. 이 두 기술의 존재로 현재 개발트렌드가 Model1에서 Model2로 변경되었는데, 그 구조가 어떻게 바뀌었는지에 대해 알아보며 그 이해를 더해보려한다.


<br>

**참고했던 블로그 포스트**

-   [https://gmlwjd9405.github.io/2018/11/04/servlet-vs-jsp.html](https://gmlwjd9405.github.io/2018/11/04/servlet-vs-jsp.html)

<br>

---
### **MODEL1 Pattern**

약 10년전, 2000년대까지만해도 가장 많이 쓰였던 방식이다. 아래의 그림처럼 JSP가 사용자의 요청을 받으면 Java Bean(DTOm DAO)을 호출하여 동적인 페이지를 구현하는 구조이다.

<br>

![그림1](/assets/img/dev\web\mvc\2.png)

<br>

> MODEL1 구현과정

- JSP로 작성된 프로그램은 내부적으로 WAS에서 Servlet 파일로 변환
- JSP태그를 분해하고 추출하여 다시 순수한 HTML 웹 페이지로 변환
- 클라이언트로 응답

<br>

> 특징

- 배우기쉽고 개발속도가 빠르다
- 프레젠테이션 로직(View)과 비지니스 로직(Controller)이 혼재한다.
- JSP코드가 복잡해져 유지보수가 어려워진다.

