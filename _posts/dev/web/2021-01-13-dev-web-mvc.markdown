---
layout: post
title: Web 9. Mode1패턴과 Model2패턴
subtitle: Model1과 Model2
categories: programming
tags: programming web
comments: true
published: true
header-img: img\dev\web\mvc\1.png
---

해당 포스팅은 [https://zunoxi.tistory.com/97?category=816528](https://zunoxi.tistory.com/97?category=816528)으로 이전되었습니다.

<!--
## 개요
> Mode1패턴과 Model2패턴에 대한 정리
  
- 목차
	- [`MVC Pattern`](#mvc-pattern)
	- [`MODEL1 Pattern`](#model1-pattern)
	- [`MODEL2 Pattern`](#model2-pattern)
  
## Model1 and Model2
---
바로 전 포스팅에서 서블릿과 JSP에 대해 알아봤다. 이 두 기술의 존재로 현재 개발트렌드가 Model1에서 Model2로 변경되었는데, 그 구조가 어떻게 바뀌었는지에 대해 알아본다.


<br>

**참고했던 블로그 포스트**

-   [https://gmlwjd9405.github.io/2018/11/04/servlet-vs-jsp.html](https://gmlwjd9405.github.io/2018/11/04/servlet-vs-jsp.html)
-   [https://coding-factory.tistory.com/69](https://coding-factory.tistory.com/69)

<br>

---
### **MVC Pattern**

MVC 패턴은 `Model, View, Controller`의 앞글자를 따서 붙인 소프트웨어 디자인 패턴이다. 차례로 각 기능으 살펴보자면 다음과 같다.

- **Model** : 백그라운드에서 동작하는 로직 처리
- **View** : 사용자가 보게 될 결과 화면을 출력
- **Controller** : 사용자의 입력처리와 흐름제어 담당

<br>

MVC패턴에는 `Model1방식과 Model2 방식`이 있으며 이는 JSP로 구성할 수 있는 웹 어플리케이션의 아키텍처에서 로직과 출력을 모두처리하는지(Model1), JSP에서는 출력만 처리하는지(Model2)에 따라 분류된다.

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

<br>

---
### **MODEL2 Pattern**

JSP와 Servlet을 모두 사용하여 프레젠테이션 로직(View)과 비지니스 로직(Controller)을 분리한다.

<br>

![그림1](/assets/img/dev\web\mvc\3.png)

<br>

> MODEL2 구성

- View : 보여지는 부분으로 HTML이 중심이되는 JSP사용
- Controller : 다른 자바 클래스에 데이터를 넘겨주는 부분으로 Java코드가 중심이 되는 Servlet을 사용
- Model은 Java Beans로, DTO와 DAO를 통해 Data Storage에 접근
  - 참고
    - DAO(Data Access Object) : DB를 사용해 데이터를 조회하거나 조작하는 기능을 전담하도록 만든 오브젝트
    - DTO(Data Transfer Object) : 계층간(컨트롤러, 뷰, 비지니스 계층 등) 데이터 교환을 위한 자바빈즈

<br>

> 특징

- 개발 확장이 비교적 용이하다.
- 각 계층이 모듈화 되어있어 유지보수가 편하다.

<br>


이번 포스티엥서는 웹 개발자로서 반드시 알아야할 `MVC패턴` 그리고 `Model1과 Model2`의 구성과 특징에 대해 알아봤다. 최근에는 MVC의 **View와 Model사이의 의존성**이 높아 `MVVM`(Model+View+View Model), `MVP` 등의 패턴이 MVC에서 파생되어 유행하고 있다. 

특히 해당 패턴들은 **React.js와 Vue.js의 유행**에 따라 더욱 핫해지는 패턴으로 분류된다. View Model의 설계가 슆지 않다는 단점이 있지만 추가적으로 함께 공부한다면 트렌드를 이해하는데 더욱 도움이 될 것으로 보인다. 관련 장단점은 링크(https://beomy.tistory.com/43)를 참고하면 좋을 것 같다.
