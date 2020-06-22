---
layout: post
title:  "Jar와 War"
subtitle:   "웹프로그래밍의 배포"
categories: programming
tags: programming web
comments: true
header-img: img/dev/web/jar/java.jpg
---

## 개요
> WAS를 이용해서 웹 서비스를 배포하다 보니 JAR, WAR 파일들이 서로 헷갈려 짚고 넘어가 보려고 한다.

### JAR(Java Archive)
---
 
- class와 같은 Java의 리소스와 속성 파일, 라이브러리 기타 파일들이 포함되어 있다. 

즉, JAVA 어플리케이션이 구동될 수 있도록 압축한 파일이라고 할 수 있다. 

플랫폼에 귀속되는 점을 제외하면 WINZIP 파일과 동일하다고 할 수 있다. 

원하는 구조로 구성이 가능하며 JDK에 포함하고 있는 JRE 갖고도 실행이 가능하다.
 

### WAR(Web Application Archive)
---

sevlet / jsp 컨테이너에서 배치할 수 있는 웹 애플리케이션 압축파일이다.

Servlet Context 접근을 위한 모든 파일들을 패키지화 해서 제공해준다.

웹 관련된 자원들만 포함하고 있어 웹 애플리케이션의 간편한 배포를 도와준다.

. jar파일과 달리 WEB-INF 디렉터리로 사전 정의된 구조를 사용한다.

실행하려면 웹서버나 WAS가 필요하다. 

 
jar파일은 자바 프로젝트용 war는 웹 프로젝트용으로 구분할 수 있을 것 같다.

또한, 일반적으로 `외장 WAS`를 사용하는 경우에는 `WAR파일`을, `내장 WAS`를 사용하는 경우 `JAR`파일을 사용한다고 한다.

필자 같은 경우는 스프링 부트 프로젝트를 배포할 때 WAR파일을 사용했었는데,

사실 스프링부트는 내장 톰캣을 지원해줘서 JAR로 배포했어도 됐을 것 같다.

 

아래 포스팅을 보며 해당 내용을 정리함.

[`링크`](https://ifuwanna.tistory.com/224)