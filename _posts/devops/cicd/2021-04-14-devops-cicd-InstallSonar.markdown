---
layout: post
title: CI/CD 9. CentOS7 Sonarqube(소나큐브) 설치
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
	- [`1. JAVA 설치`](#3.-JAVA-설치)
	- [`2. DB(PostgreSQL)설치`](#4.-DB(PostgreSQL)설치)
	- [`3. Sonarqube 설치`](#5.-Sonarqube-설치)
  
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

<span style="color:red">※</span> 필자는 root로 접근해서 작업했다. root 권한이 없는 경우 모든 명령어 앞에 sudo 를 붙이고 작업하는것을 추천한다.


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

```java
yum list java*jdk-devel 
```

<br>

![그림2](/assets/img/devops/cicd/sonarqube/2.png)

<br>

다음과 같이 확인되며 필자는 테스트를 위해 8버전과 11버전을 모두 설치한 것으로 확인된다.

<br>

1.8 버전만 설치를 원할 시 다음 명령어를 입력한다.


```java
# yum install java-1.8.0-openjdk-devel.x86_64 -y

# which java
/usr/bin/java

# readlink -f /usr/bin/java
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64/jre/bin/java

# vi .bash_profile             #아래내용 추가
 
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64/jre/bin/java
export JAVA_HOME

#source .bash_profile

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



```
# yum install https://download.postgresql.org/pub/repos/yum/9.6/redhat/rhel-7.8-x86_64/pgdg-redhat-repo-latest.noarch.rpm

# yum list | grep ^postgresql

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

pg_hba.conf 파일에서 `peer` -> `trust`, `ident` -> `md5` 로 변경한다.

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

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
# postgresql 접근 및 User 계정 생성
su - postgres

psql

create user sonar;


# sonar 계정 password 설정 및 DB생성
ALTER USER sonar WITH ENCRYPTED password 'Sonar123!@#';
CREATE DATABASE sonar OWNER sonar;

# postgresql shell 종료
\q
```
<br>

> 시스템 vm.max_map_count 설정 값 확인

프로세스가 사용할 수 있는 메모리 맵 영역의 최대 수를 지정한다. 기본값이 65530이나 sonarqube의 원할한 운영을 위해 262144로 늘리는것을 권장한다.

```
sysctl -a | grep vm.max_map_count
sysctl -w vm.max_map_count=262144
```

<br>
<br>

---

### **5. Sonarqube 설치**

<br>

>sonarqube 설치

```
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.2.zip
unzip sonarqube-7.2.zip    #  unzip 프로그램이 없는경우 unzip 설치가 우선시 되어야한다.
mv sonarqube-7.2 sonarqube
```
<br>

> sonarqube 유저 등록 및 폴더권한 부여

```
adduser sonar
passwd soanr  #soanr 계정 PW 설정
chown -R sonar /opt/sonarqube/
```
<br>

> sonarqube와 DB연동 설정

```
# vi /opt/sonarqube/conf/sonar.properties


# 아래 내용 추가(혹은 기존내용 주석해제 및 수정)
sonar.jdbc.username=sonar					#postgresql sonar 계정
sonar.jdbc.password=Sonar123!@#				#postgresql sonar 계정의 pw
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonar?useUnicode=true&characterEncoding=utf8&useSSL=false
```

<br>

sonarqube7.2 버전과 jdbc연동 시 jdbc.url 부분 유의사항([참고](https://docs.sonarqube.org/7.2/Requirements.html))
- UTF8로 문자 인코딩 필요
- SSL 미사용 시 에는 useSSL=false, 사용 시에는 useSSL=true 설정이 필요

<br>

> 방화벽 오픈

sonarqube는 9000번 포트에서 동작하기때문에 방화벽에서 9000번 포트를 열어줘야한다.

```
firewall-cmd --permanent --add-port=9000/tcp
systemctl restart firewalld
firewall-cmd --list-all
```

<br>

---

### **6. Sonarqube 실행**

<br>


soanrqube를 어떤 유저로 실행할건지 설정해준다.

```
# /opt/sonarqube/bin/linux-x86-64/sonar.sh
RUN_AS_USER=sonar
```

<br>


자, 이제 sonarqube를 실행해보자

```
cd /opt/sonarqube/bin/linux-x86-64
./sonar.sh start
./sonar.sh status
./sonar.sh console
```

<br>

혹시 실행 시 오류가 난다면 sonar user의 접근이 막힌 temp파일이 생겨서 그럴 수 있으니 다시한번 권한 부여를 해준다.

```
chown -R sonar:sonar sonarqube
```

<br>

다음으로 웹에서 `http://localhost:9000` (혹은 sonarqube 서버ip) 입력한다.

![그림3](/assets/img/devops/cicd/sonarqube/3.png)

<br>

위와 같은 화면이 나오면 설치가 완료된 것이다 :)



<br>


> 참고 1. sonarqube를 시스템으로 등록

```
# vi /usr/lib/systemd/system/sonar.service

[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=soanr
Group=sonar
Restart=always

[Install]
WantedBy=multi-user.target
```

<br>

```
systemctl daemon-reload
systemctl start sonar.service
```

<br>

---

### **7. Sonarqube 7.9 설치**


<br>

현재 포스팅시점인 2021년 5월 3일 기준, `Sonarqube의 LTS 버전은 7.9 버전`이다. 따라서 장기간 사용을 고려 시 Java 11버전을 별도로 설치하더라도 7.9를 사용할 이유는 있다. 필자는 7.2 버전을 설치 후 7.9로 재설치를 해봤다. 관련 내용을 추가로 기록한다. 

<br>

> java 11 설치

<br>

```java
# yum install java-11-openjdk-devel -y

# alternatives --config java                  # java 설치현황 확인

2 개의 프로그램이 'java'를 제공합니다.

  선택    명령
-----------------------------------------------
*+ 1           java-1.8.0-openjdk.x86_64 (/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64/jre/bin/java)
   2           java-11-openjdk.x86_64 (/usr/lib/jvm/java-11-openjdk-11.0.11.0.9-1.el7_9.x86_64/bin/java)

현재 선택[+]을 유지하려면 엔터키를 누르고, 아니면 선택 번호를 입력하십시오:
```

위 결과에서 java-11 버전에 대한 경로(/usr/lib/jvm/java-11-openjdk-11.0.11.0.9-1.el7_9.x86_64/bin/java)를 복사해둔다.

<br>

> PostgreSQL DB 스키마 삭제

<br>

sonarqube 7.9와 postgreSQL 9.6은 호환되기때문에 이미 위의 글내용대로 설치했다면 Postgres 삭제 혹은 추가 설치는 불필요하다. 생성했던 sonar database만 삭제하고 다시 만든다. (필자는 postgreSQL을 잘 모르지만 vacuum을 사용하는 방법도 있다고 한다..)

```
# su - postgres
# psql
# drop database sonar;
# CREATE DATABASE sonar OWNER sonar;
```

<br>

> sonaqube 7.9 버전 다운로드 및 설치

<br>

```
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.9.6.zip
unzip sonarqube-7.9.6.zip
mv sonarqube-7.9.6 sobarqube
```

<br>

> sonarqube 설정

```
# vi /opt/sonarqube/conf/wrapper.conf

wrapper.java.command=/usr/lib/jvm/java-11-openjdk-11.0.11.0.9-1.el7_9.x86_64/bin/java            # 주석해제 후 수정 or 추가


# vi /opt/sonarqube/conf/sonar.properties


## 아래 내용 추가(혹은 기존내용 주석해제 및 수정)
sonar.jdbc.username=sonar					#postgresql sonar 계정
sonar.jdbc.password=Sonar123!@#				#postgresql sonar 계정의 pw
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonar?useUnicode=true&characterEncoding=utf8&useSSL=false

# vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
RUN_AS_USER=sonar
```

<br>

> sonarqube 실행

<br>


```
#chown -R sonar:sonar sonarqube

# cd /opt/sonarqube/bin/linux-x86-64
# ./sonar.sh start
# ./sonar.sh status
# ./sonar.sh console
```


<br>

- 참고

 이외의 재설치 시 의 다양한 오류를 맞이 할 수 있는데. web.log 파일까지 참고 혹은 서버 reboot(max file descripter관련 에러 의심)하는것을 추천한다. sonarqube 설치시의 오류는 대부분 log파일을 확인하면 원인을 찾을 수 있다. 또한, SELinux 설정도 확인해볼 필요가 있다.