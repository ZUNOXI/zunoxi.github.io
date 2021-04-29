---
layout: post
title: CI/CD 9. CentOS 7 Sonarqube(소나큐브) 설치하기
subtitle: CentOS 7 Sonarqube(소나큐브) 설치
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/devops/cicd/sonarqube/main.png
---

## 개요
> CentOS 7 환경에 Sonarqube 설치하기
  
- 목차
	- [`1. Git`](#1.-Git)
	- [`2. Git 호스팅서비스`](#2.-Git-호스팅서비스)
	- [`3. 호스팅서비스 선택`](#3.-호스팅서비스-선택)
  
## Sonarqube 설치
---
오랜만에 삽질포스팅을 한다. 거의 2일 꼬박 밤을새우며 Sonarqube 설치에 매달렸는데, 여러가지 시도끝에 해결방법을 찾은것 같다. Sonarqube 다른 주목받는 Tool들에 비해 국내에 상대적으로 덜 알려져 있는건지, 필자가 잘 못찾는건지 해외 포럼에서 해결방법을 찾았다. 혹시라도 필자와 같은 환경에 있는 사람이 있다면 이 글이 참고가 되길바라며 CentOS7 환경의 Sonarqube 설치과정을 포스팅한다.



<br>


---

### **1. Sonarqube?**

<br>

설치에 앞서 _**sonarqube가 어떤 툴인지**_ 알아보면 좋을 것 같다. soanrqube는 나무위키에 나와있는 것 처럼 지속적으로 코드의 품질을 높이고 유지 보수하는데 도움을 주는 `소스코드 정적 분석 프로그램`이다. 해당 프로그램은 **소나소스(sonarsource)** 가 개발하였으며 중복코드, 코딩 표준, 유닛테스트, 코드 복잡도 등의 버그 및 보안 취약점의 보고서를 제공한다. 

<br>

필자는 CI/CD 중 Jenkins로 Maven 빌드하는 과정에 `sonarqube를 사용하여 코드품질을 검사`하고, 일정수준 이하의 품질이라면 배포하지 않게끔 하는 목적으로 soanrqube를 설치하고 Jenkins와 연동하려 한다.

<br>


![그림1](/assets/img/devops/cicd/sonarqube/1.png)

<br>

---

### **2. 설치환경**

<br>

이번 sonarqube를 설치하며 언어 및 프로그램들간의 버전 호환성과 로그를 철저히 살펴보는것의 중요함을 다시 한번 느꼈다. 우선 필자는 Jenkins 설치를 위하여 `java 1.8(openjdk 1.8)`을 설치한 상황이다. 최초에는 `sonarqube 7.9 버전`을 설치했으나 _**정상적으로 설치가 되지않았다.**_ 이후 로그를 보며 확인해보니 해당버전은 `java11을 사용`해야 하는것을 확인했다.

<br>

```java
[root@hspjenddcp01 linux-x86-64]# ./sonar.sh console
Running SonarQube...
wrapper  | --> Wrapper Started as Console
wrapper  | Launching a JVM...
jvm 1    | Wrapper (Version 3.2.3) http://wrapper.tanukisoftware.org
jvm 1    |   Copyright 1999-2006 Tanuki Software, Inc.  All Rights Reserved.
jvm 1    | 
jvm 1    | 
jvm 1    | WrapperSimpleApp: Encountered an error running main: java.lang.IllegalStateException: SonarQube requires Java 11 to run
jvm 1    | java.lang.IllegalStateException: SonarQube requires Java 11 to run
jvm 1    |      at com.google.common.base.Preconditions.checkState(Preconditions.java:173)
jvm 1    |      at org.sonar.application.App.checkJavaVersion(App.java:92)
jvm 1    |      at org.sonar.application.App.start(App.java:57)
jvm 1    |      at org.sonar.application.App.main(App.java:96)
jvm 1    |      at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
jvm 1    |      at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
jvm 1    |      at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
jvm 1    |      at java.lang.reflect.Method.invoke(Method.java:498)
jvm 1    |      at org.tanukisoftware.wrapper.WrapperSimpleApp.run(WrapperSimpleApp.java:240)
jvm 1    |      at java.lang.Thread.run(Thread.java:748)
wrapper  | <-- Wrapper Stopped
```


<br>

이는 OS에 java11 버전도 설치하고 해당 JAVA_HOME으로 sonarqube 설정 경로를 잡아줘도 무방하겠지만, 혹시라도 jenkins의 연동과의 문제점이 생길까봐 1.8을 지원하는 soanrqube 7.2 버전을 설치했다. 여러개의 JAVA를 설치하고 경로를 확인하는 것은 [링크](https://jeanette.tistory.com/82)의 포스팅을 참고하면 될 것 같다. 설치를 완료한 시점 필자의 설치환경은 다음과 같다.


- `Server` : CentOS 7 [CentOS Linux release 7.9.2009 (Core)]
- `Jave version` : openjdk version "1.8.0_292"
- `Sonarqube version` : sonarqube 7.2 ver
- `DB version` : Postgresql 9.6 ver (mysql 5.7 호환되는것 확인)

