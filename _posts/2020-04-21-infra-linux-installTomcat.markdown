---
layout: post
title: Server 1. centos7에 apache tomcat 설치
subtitle: centos7에 apache tomcat 설치
categories: infra
tags: infra server
comments: true
published: true
header-img: img/infra/server/hourly/tomcat.png
---

## 개요
> `centos7`에 apache tomcat 설치
  
- 목차
	- [`웹 서버와 WAS 차이`](#웹-서버와-wasweb-application-server)
	- [`웹 서버와 WAS 연동`](#웹-서버와-was-연동)
  
## Tomcat Linux 설치
---
윈도우에 톰캣설치하는것은 매우 간단한데 리눅스는 ubuntu만 다뤄봐서 그런지 centos는 약간의 다름이 느껴진다. 이번 포스팅에는 centos7.5환경에 apache tomcat 9.x 버전의 설치를 다뤄본다.


<br>


---

**1. tomcat 사이트 접속**

톰캣 공식사이트에서 톰캣 9.0.35 버전의 링크를 복사한다.

[https://tomcat.apache.org/download-90.cgi](https://tomcat.apache.org/download-90.cgi)

(해당 페이지에서 core에 "apache-tomcat-9.0.35.tar.gz"에 오늘쪽 마우스를 눌러 링크를 복사한다.)

<br>

![그림1](/assets/img/infra/server/install/1.png)

<br>

**2\. 의존성 주입**

```
 yum install -y wget   
```

\* 참고 : GNU Wget는웹서버로부터콘텐츠를가져오는컴퓨터프로그램으로, GNU프로젝트의일부. 

**3\. 톰캣 설치 및 압축풀기**

톰캣을 언패키징할 폴더로 이동하여 아래 명령어를 입력한다.

(아무곳에서나 다운로드 받아도 되나 원활한 환경변수 설정을 위해 /usr/local 에서 압축을 풀어준다)

**(1) 다운로드 **

```
wget http://mirror.navercorp.com/apache/tomcat/tomcat-9/v9.0.35/bin/apache-tomcat-9.0.35.tar.gz
```

<br>

![그림2](/assets/img/infra/server/install/2.png)

<br>

**(2) 압축풀기**

다운로드 받은 위치에서 해당 파일의 압축을 풀어준다.

```
tar xvfz apache-tomcat-9.0.35.tar.gz
```

**4\. 톰캣 실행**

쉘에 아래 명령어를 입력한다.

```
/usr/local/apache-tomcat-9.0.35/bin/startup.sh 
```

<br>

![그림3](/assets/img/infra/server/install/3.png)

<br>

이런식으로 진행되면 톰캣이 실행된것이다.

<br>

![그림4](/assets/img/infra/server/install/4.png)

<br>

아파치 톰캣의 기본 connect port는 8080번이므로 

호스트의 ip : 8080 포트로 접속했을때 위와 같은 고양이 화면이 나타나면 정상적으로 실행중이라 판단할 수 있다.

**5\. 자동실행등록 (서비스 등록)**

매번 해당경로까지 이동해서 켜주기에는 번거롭기때문에

서버 실행 시 자동으로 실행될 수 있도록 서비스를 등록해 본다.

먼저 실행중이던 톰캣을 중지한다.

```
/usr/local/apache-tomcat-9.0.35/bin/catalina.sh stop
```

다음, 톰캣 서비스를 만들어주고 아래와 같이 입력한다.

```
vi /usr/lib/systemd/system/tomcat.service
```

```
[Unit]
Description=tomcat9
After=network.target syslog.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/local/victolee/jdk1.8
User=root
Group=root

ExecStart=/usr/local/apache-tomcat-9.0.35/bin/startup.sh
ExecStop=/usr/local/apache-tomcat-9.0.35/bin/shutdown.sh

UMask=0007
RestartSec=10
Restart=always



SuccessExitStatus=143


[Install]
WantedBy=multi-user.target
```

이제 다음 일련의 과정을 진행하면 톰캣이 서버 재부팅시에도 정상적으로 작용되는것을 볼 수 있다.

```
systemctl enable tomcat.service
systemctl start tomcat.service

reboot
ps -ef | grep tomcat
```

<br>

![그림5](/assets/img/infra/server/install/5.png)

<br>

다음과 같이 출력되면 서비스 자동 실행도 완료 된것이다.

---