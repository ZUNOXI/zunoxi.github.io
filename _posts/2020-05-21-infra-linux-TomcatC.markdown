---
layout: post
title: Server 3. Centos7 Apache와 Tomcat 연동
subtitle: Webserver와 Was 연동하기
categories: infra
tags: infra server
comments: true
published: true
header-img: img/infra/server/hourly/tomcat.png
---

## 개요
> `Centos7` Apache(Webserver)와 Tomcat(WAS) 연동하기
  
- 목차
	- [`웹 서버와 WAS 차이`](#웹-서버와-wasweb-application-server)
	- [`웹 서버와 WAS 연동`](#웹-서버와-was-연동)
  
## Webserver with WAS
---
우리가 일반적으로 웹화면을 구성할때 정적인 html, css를 처리하는 기능은 **웹서버**만으로도 구현이 가능하다. 단, 비지니스 로직을 수행해야 하는 동적 페이지를 처리시에는 **WAS**(Web ApplicationServer)라는것이 필요하다. 이 WAS라는것이 단독으로도 정적, 동적페이지 모두를 구현을 할 수 있으나 과도한 부하가 발생할 수 있어서 부하분산이 필요하다. 


<br>





이와 별개로, 8080포트에는(WAS의 포트)에는 `방화벽을 설정`하여 어플리케이션 로직에 직접적 접근을 방지 하는 등, 여러가지 이유로 `web server와 WAS를 연동하여 동시에 운영`하는것이 합리적인 방법으로 여겨진다. 

<br>

---

### 웹 서버와 WAS(Web Application Server)

<br>

`웹 서버는 정적인 컨텐츠`( html, css, js )를 제공하는 서버이다. (ex) Apache, Nginx) `WAS`는 웹서버의 정적 자원처리 역할뿐만아니라 DB 조회 혹은 비지니스 로직을 처리하는 `동적 컨텐츠`도 구동이 가능한 서버이다.

일반적으로 구조는 `웹서버가 앞단`에와서 서버 혹은 도메인으로 넘어온 요청을 연동된 WAS 포트로(웹서버와 WAS가 같은 서버에 있는경우) 넘겨주게 구성되어있다.

필자의 작업환경에서의 주 언어는 JAVA이므로 WAS는 오픈소스인 아파치 톰캣을 사용한다. (사용 환경 : CentOS 7.5, Apache web server(httpd) 2.4.6, Tomcat 9.0.8)


> 참고로 아파치와 아파치톰캣은 전혀다르다. 우리가 일반적으로 많이 듣게되는 `아파치`는 Apache foundation에서 만든 `Apache Webserver`이고, `톰캣`은 마찬가지로 Apache 재단에서 만든 `Apache Tomcat(WAS)`이다. 필자는 처음에 이런 미들웨어들을 배우며 상당히 헷갈렸던 기억이난다. 

<br>

_**※ 실무 같은경우 사내망이 아닌 외부에서도 접근할 수 있는 구조로 설계를 해야하는 조건에서는 web-server서버를 DMZ에 별도로 분리함으로써 보안을 강화하는 방법이 있다.**_

<br>

---
### 웹 서버와 WAS 연동

<br>

#### **1\. 웹서버 설치**

CentOS환경에서는 apache가 `httpd(http 웹서버)`로 명칭된다.  (ubuntu에서는 apache라는 이름으로 사용) 
여기서, httpd는 http웹서버를 구동할 수 있는 데몬이다.

해당 httpd 패키지를 먼저 설치한다. (설치 되어있는경우는 패스)

```
yum update

yum install -y httpd

httpd -version  # 정상 설치 여부 확인
```

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/2.png)

<br>

#### **2\. Tomcat 설치**

필자가 설치한 최초 아파치버전은 2.4.6과 톰캣 9.0.35 버전이랑은 계속 ajp 통신오류가 났다.(원인미상..)

그래서 아래 포스팅을 참고하되 9버전대를 설치한다면 `9.0.8 버전`으로 설치를 추천한다.

[https://zunoxi.tistory.com/41](https://zunoxi.tistory.com/41)

<br>

#### **3\. tomcat-connector(mod\_jk) 설치**

web-server와 tomcat을 연동시키기 위해 `tomcat-connector를 설치`해준다.

**(1) 의존성 주입**

```
wget -c http://mirror.navercorp.com/apache/tomcat/tomcat-connectors/jk/tomcat-connectors-1.2.46-src.tar.gz
```
<br>

**(2) 압축 풀기 및 폴더 이동**

```
tar xvf tomcat-connectors-1.2.46-src.tar.gz

mv tomcat-connectors-1.2.46-src/ /usr/local/src

cd /usr/local/src/tomcat-connectors-1.2.46-src/native/
```
<br>

**(3) 빌드**

빌드 전 먼저 해당 유틸들이 설치 되어있는지 확인

```
yum -y install gcc gcc-c++ httpd-devel
```

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/3.png)

<br>

이제 빌드를 해본다. (위치는 cd /usr/local/src/tomcat-connectors-1.2.46-src/native/)

```
./buildconf.sh
```

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/4.png)

```
./configure --with-apxs=/usr/bin/apxs
```

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/5.png)

```
make

make install
```

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/6.png)

<br>

**(4) mod\_jk 파일 확인**

```
ls /etc/httpd/modules/ | grep mod_jk
```

![그림6](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/7.png)

<br>

#### **4\. 기타 설정 파일 수정**

**(1) httpd.conf 파일 수정**

```
vi /etc/httpd/conf/httpd.conf
```

`httpd.conf` 파일 안에 다음 내용을 추가 (가상호스트 추가)

```
<VirtualHost *:80>
    DocumentRoot "/u01/zunoxi"  # 기본페이지 루트
    ServerName zuno.com # 도메인 지정
    ErrorLog "logs/zuno.com-error.log"
    CustomLog "logs/zuno.com-access.log" common
    JkMount /* tomcat # 해당 포트 혹은 도메인으로 요청이 들어왔을때 연결할 톰캣 마운트
</VirtualHost>
```

이때 `가상호스트(VirtualHost)` 설정 및 `ServerName`을 별도 설정하면 경우는 하나의 서버에서 여러개의 톰캣을 구동시킬 시, 포트번호가아닌 도메인 네임으로 각 톰캣에 접근 할 수 있게하는 기능을 한다. 

해당 파일에 아래의 내용도 추가 (주석처리 되어있다면 주석해제)

```
LoadModule jk_module modules/mod_jk.so
```

<br>

**(2) workers.properties 파일 수정**

```
vi /etc/httpd/conf/workers.properties
```

![그림7](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/8.png)

위 httpd.conf 파일의 가상호스트 설정에 `JkMount` /\* tomcat 부분에서 tomcat이라는 이름으로 마운트 설정을 했기에 workers.properties에는 위 처럼 설정해주면 된다.

`8009 포트설정`은 웹서버에서 `Ajp 통신포트`로 8009로 연결되어 요청을 톰캣으로 보내주는 역할을 한다. 

<br>

**(3)  mod\_jk.conf 파일 수정**

```
vi /etc/httpd/conf.modules.d/mod_jk.conf
```

(파일이 없을수도 있다. 그냥 만들면 된다)

```
<IfModule jk_module>
    JkWorkersFile conf.d/workers.properties  # workers.properties를 읽어 들이겠다는 뜻 
    JkLogStampFormat "[%a %b %d %H:%M:%S %Y] "
    JkLogFile logs/mod_jk.log
    JkLogLevel info
    JkShmFile run/mod_jk.shm
</IfModule>

```

※ 해당과정에서 처음에는 아래와 같은 에러가 났다.

![그림8](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/9.png)

<br>

```
\[Tue May 19 16:09:13.388 2020\] \[20094:140649420454016\] \[error\] extension\_fix::jk\_uri\_worker\_map.c (580): Could not find worker with name 'tomcat' in uri map post processing.

\[Tue May 19 16:09:13.416 2020\] \[20094:140649420454016\] \[info\] init\_jk::mod\_jk.c (3591): mod\_jk/1.2.46 initialized

\[Tue May 19 16:09:13.416 2020\] \[20094:140649420454016\] \[error\] extension\_fix::jk\_uri\_worker\_map.c (580): Could not find worker with name 'tomcat' in uri map post processing.

\[Tue May 19 16:09:27.945 2020\] \[20098:140649420454016\] \[info\] jk\_handler::mod\_jk.c (2999): Could not find a worker for worker name=tomcat

\[Tue May 19 16:09:28.282 2020\] \[20096:140649420454016\] \[info\] jk\_handler::mod\_jk.c (2999): Could not find a worker for worker name=tomcat
```


이는 mod\_jk.conf에 최초 설정시

_**<IfModule mod\_jk.c>**_

JkWorkersFile conf/workers.properties

JkShmFile run/mod\_jk.shm

JkLogFile logs/mod\_jk.log

JkLogLevel info

JkLogStampFormat "\[%y %m %d %H:%M:%S\] "

_**</IfModule>**_

<IfMoudule mod\_jk.c> 

라고 설정했기 때문이다....<IfModule jk\_module> 라고 설정하는 것이 맞다.

<br>

#### **5\. httpd 재기동 및 정상 접속 확인**

```
systemctl stop httpd.service

systemctl start httpd.service
```

여기서 정상적으로 연결되고 이런식으로 ip(포트번호 없이) 혹은 domain으로 접속했을때, 톰캣의 웹페이지로 바로 접속이 된다면 성공이다.

필자는 호스트에 도메인을 지정해놔서 도메인으로 들어가는것으로 확인했다.

> 참고 : C:\\Windows\\System32\\drivers\\etc 에 hosts 파일에 특정 ip의 도메인을 등록하고 테스트해 볼 수있다.

![그림9](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/server/tomcatC/10.png)

위 사진 처럼 웹서버와 WAS가 정상적으로 연동되어 있는것을 알수 있다.

★ 다음 포스팅에서는 SSL 인증으로 HTTPS 보안서버를 구축해볼 예정 :)