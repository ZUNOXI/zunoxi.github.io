---
layout: post
title: CI/CD 9. CentOS7 Sonarqube(소나큐브) 설치하기
subtitle: CentOS7 Sonarqube(소나큐브)7.2 설치
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/devops/cicd/sonarqube/main.png
---

## 개요
> CentOS 7 환경에 Sonarqube 7.2 설치하기
  
- 목차
	- [`1. Git`](#1.-Git)
	- [`2. Git 호스팅서비스`](#2.-Git-호스팅서비스)
	- [`3. 호스팅서비스 선택`](#3.-호스팅서비스-선택)
  
## Sonarqube 설치
---
오랜만에 삽질포스팅을 한다. 거의 2일 꼬박 밤을새우며 Sonarqube 설치에 매달렸는데, 여러가지 시도끝에 해결방법을 찾은것 같다. Sonarqube 다른 주목받는 Tool들에 비해 국내에 상대적으로 덜 알려져 있는건지, 필자가 잘 못찾는건지 해외 포럼에서 해결방법을 찾았다. sonarqube는 버전에 따라 지원하는 언어 혹은 툴이 다르기 때문에 **`공식문서 참고가 필수`** 이다. 필자와 같은 환경에 있는 사람이 있다면 이 글이 참고가 되길바라며 CentOS7 환경의 Sonarqube 설치과정을 포스팅한다.


<br><br>


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

이번 sonarqube를 설치하며 언어 및 프로그램들간의 버전 호환성과 로그를 철저히 살펴보는것의 중요함을 다시 한번 느꼈다. 우선 필자는 Jenkins 설치를 위하여 `java 1.8(openjdk 1.8)`을 설치한 상황이다. 최초에는 `sonarqube 7.9 버전`을 설치했으나 _**정상적으로 설치가 되지않았다.**_ 이후 로그를 보며 확인해보니 해당버전은 `java11을 사용`해야 하는것을 확인했다. 관련 [Sonarqube 7.9 지원 공식문서](https://docs.sonarqube.org/7.9/requirements/requirements/)를 참고하면 좋을 것 같다.

<br>

```java
# ./sonar.sh console
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

이는 OS에 java11 버전도 설치하고 해당 JAVA_HOME으로 sonarqube 설정 경로를 잡아줘도 무방하겠지만, 혹시라도 jenkins의 연동과의 문제점이 생길까봐 1.8을 지원하는 soanrqube 7.2 버전을 설치했다. (현재 Sonarqube 7.9 version이 LTS 버전임은 참고) 여러개의 JAVA를 설치하고 경로를 확인하는 것은 [링크](https://jeanette.tistory.com/82)의 포스팅을 참고하면 될 것 같다. 설치를 완료한 시점 필자의 설치환경은 다음과 같다.


- `Server` : CentOS 7 [CentOS Linux release 7.9.2009 (Core)]
- `Jave version` : openjdk version "1.8.0_292"
- `Sonarqube version` : sonarqube 7.2 ver
- `DB version` : PostgreSQL 9.6 ver (mysql 5.7 호환되는것 확인)

<br>

※ 필자는 root로 접근해서 작업했다. root 권한이 없는 경우 모든 명령어 앞에 sudo 를 붙이고 작업하는것을 추천한다.


<br>

---

### **3. JAVA 설치**

<br>

필자는 앞서 언급했던것처럼 Jenkins 설치 시 java 1.8 버전으로 자바를 설치했다. 주 업무 환경에서 java 1.8 버전을 사용하고 있기때문인데, 설치방법은 아래와 같다.

<br>

> yum 패키지 업데이트 

```
yum update
```

<br>

> yum으로 설치 할 수 있는 openjdk 목록 확인

<br>

```java
yum list java*jdk-devel 
```

<br>

![그림2](/assets/img/devops/cicd/sonarqube/2.png)

<br>

다음과 같이 확인되며 필자는 테스트를 위해 8버전과 11버전을 모두 설치한 것으로 확인된다.

<br>

1.8 버전만 설치를 원할 시 다음 명령어를 입력한다.

<br>

```
yum install java-1.8.0-openjdk-devel.x86_64 
```

<br>

버전확인 시 아래와 같이 나오면 정상설치 완료이다.
```
# java -version
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
OpenJDK 64-Bit Server VM (build 25.292-b10, mixed mode)
```
<br>

---

### **4. DB(PostgreSQL)설치**

<br>

Sonarqube를 사용하려면 sonarqube를 운영하는데 필요한 데이터들을 저장할 DB가 별도로 필요하다. 일반적으로 PostgreSQL을 사용하고 `sonarqube 7.2에서는 MySQL도 사용가능`하여 테스트를 해봤고 정상적으로 연동되는것을 확인했다. 다만, Sonarqube에서는 MySQL로 연동하는것을 권장하지 않고 sonarqube 7.8부터는 Mysql을 지원하지 않기 때문에 `PostgreSQL 9버전 이상으로 설치`하는것이 좋아보인다.

<br>

이번 설치과정에서는 `PostgreSQL 9.6 버전을 설치`했다. 기존에 PostgreSQL 11 버전을 설치했는데, Sonarqube 7.2 버전과는 PostgreSQL 8 or 9 버전대와 호환이 가능하다고 하여 재설치 했다. 

(참고) 가장 최근버전인 sonarqube 8.8은 PostgreSQL 9.3- 9.6 부터 12 버전까지 호환이 된다. [관련링크](https://docs.sonarqube.org/latest/requirements/requirements/)

<br>

> PostgreSQL RPM 다운로드

yum package를 업데이트 한 상태이면 건너 뛰어도 되는 부분이다.

```
# yum -y install postgresql96 postgresql96-server postgresql96-contrib postgresql96-libs

# yum update -y
```
<br>

> PostgreSQL 설치 

```
yum -y install postgresql96 postgresql96-server postgresql96-contrib postgresql96-libs
```
<br>

> Init DB

```
/usr/pgsql-9.6/bin/postgresql96-setup initdb
```
<br>

> MD5-based authentication 변경

```
# vi /var/lib/pgsql/9.6/data/pg_hba.conf
```
<br>

pg_hba.conf 파일에서 peer -> trust, ident -> md5 로 변경한다.

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```
<br>

변경된 파일 내용

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
# IPv6 local connections:
host    all             all             ::1/128                 md5
```

<br>


> 서비스 시작 및 구동확인

```java
# systemctl start postgresql-9.6
# systemctl enable postgresql-9.6
# ps -ef | grep postgres   # 아래와 같이 나오면 정상
postgres 23090     1  0 11:26 ?        00:00:00 /usr/pgsql-9.6/bin/postmaster -D /var/lib/pgsql/9.6/data/
postgres 23092 23090  0 11:26 ?        00:00:00 postgres: logger process   
postgres 23094 23090  0 11:26 ?        00:00:00 postgres: checkpointer process   
postgres 23095 23090  0 11:26 ?        00:00:00 postgres: writer process   
postgres 23096 23090  0 11:26 ?        00:00:00 postgres: wal writer process   
postgres 23097 23090  0 11:26 ?        00:00:00 postgres: autovacuum launcher process   
postgres 23098 23090  0 11:26 ?        00:00:00 postgres: stats collector process  
```
<br>

> sonarqube용 계정 및 DB 생성

```
# User 계정 생성
su - postgres

creat user sonar;

# postgresql 접근 및 DB 생성
psql

ALTER USER sonar WITH ENCRYPTED password 'Sonar123!@#';

CREATE DATABASE sonar OWNER sonar;

# postgresql shell 종료
\q
```
<br>

---

### **5. Sonarqube 설치**