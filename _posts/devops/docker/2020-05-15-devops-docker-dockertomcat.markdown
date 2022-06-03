---
layout: post
title: Docker 6. Docker로 Tomcat 운영
subtitle: Docker로 Tomcat 운영하기
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

헤당 포스팅은 [https://zunoxi.tistory.com/48?category=950190](https://zunoxi.tistory.com/48?category=950190)으로 이전되었습니다.

<!--

## 개요
> `Docker` 를 활용하여 `Tomcat` 운영하기 
  
- 목차
	- [`Tomcat Dokcer image 다운`](#1-tomcat-dokcer-image-다운받기)
	- [`Tomcat 이미지 실행`](#2-tomcat-이미지-실행)
	- [`호스트에서 컨테이너로`](#3-호스트에서-컨테이너로-파일복사)
	- [`브라우저에서 확인`](#4-브라우저에서-확인)
  
## Tomcat on Docker Engine
---
쿠버네티스와 도커를 함께 배우다 보니 문뜩 `Tomcat`도 오케스트레이션 범주에 포함시켜서 예상치 못하게 WAS가 죽어버렸을 경우, `자동으로 재기동`되게끔 할 수 없을까 하는 생각이 들었다.해당이유로 이런 시스템 구축을 위해 Docker를 활용한 tomcat운영을 먼저 해보기로 했다.

<br>

---


### **1\. Tomcat Dokcer image 다운받기.**

[https://hub.docker.com/\_/tomcat](https://hub.docker.com/_/tomcat)

먼저, 위의 Docker hub에 접속하시면 다양한 톰캣의 버전이 있다. 딱히 지금 설치에 버전구분이 필요없기에 가장 최신 버전을 받기로 한다.

```
docker pull tomcat
```

![그림1](/assets/img/devops/docker/tomcat/1.png)

tomcat의 이미지 파일이 다운받아진걸 확인할 수 있다.

<br>

---

### **2\. Tomcat 이미지 실행**

실행시키는 방법은 간단하게 아래 명령어를 입력한다.

```
docker run -d --name tomcat_test -p 80:8080 tomcat

```

위 명령어에 대해 간략히 설명하자면,

**\-d** : 백그라운드 실행

**\--name** : tomcat\_test 라는 이름을 가진 톰캣 이미지기반 컨테이너를 만든다.

**\-p** : 포트번호 설정, 외부에서 80포트로 접속시 도커컨테이너의 8080으로 연결시켜준다  
(즉, 톰캣의 기본 connect port로 연결된다.)

\- 마지막 "**tomcat**" 부분은 톰캣이미지 파일에 태깅된 이미지이름이다. 이미지 ID를 입력해도 동일하게 처리된다.

![그림2](/assets/img/devops/docker/tomcat/2.png)

잘 구동되는것을 확인할 수 있다. 이제 컨테이너 내부로 들어가서 웹프로그램과 연동해 주자

<br>

---

### **3\. 호스트에서 컨테이너로 파일복사**

사실 도커에서 제공하는 `볼륨기능`을 사용하면 사전에 웹페이지의 위치를 설정하여 편하게 쓸수도 있을것이다.

볼륨관련된 내용은 다음포스팅에서 다뤄보기로 하고 우선은 컨테이너내에 호스트에 있는 웹페이지 파일을 복사하고 WAS를 이용한 웹 호스팅을 해보려한다.

명령어는 다음과 같다.

```
docker cp /u01/Devops/test/dist/index.html tomcat_test:/usr/local/tomcat/webapps/ROOT
```

(추가로 css, js 등등의 파일도 옮겨준다. 사실 한번에 폴더째로 옮기는게 가장 좋을것같다.)

위 명령어 역시 설명을 덧붙여보자면

**"docker cp \[복사할 호스트의 경로\] \[컨테이너 이름\] \[복사될 컨테이너 경로\]"**

![그림3](/assets/img/devops/docker/tomcat/3.png)

해당 컨테이너 내부 경로에 복사할 파일들이 잘 복사 되었다.

<br>

---

### **4\. 브라우저에서 확인**

보통 톰캣의 listening 포트는 `8080`번 포트이지만 우리는 사전에 도커컨테이너의 내부포트를 외부의 `80`포트(http기본포트)와 연결을 시켜놓았다.

그러므로 브라우저에 포트번호 없이 ip만을 이용하여 접속해도 설정한 페이지가 나타난다. (톰캣의 테스트만을 위해서라면 컨테이너를 만들때 8080:8080으로 맵핑하고, ip:8080을 주소창에 입력한다.)

아래페이지는 내부망으로 연결된 다른 서버의 IP만 입력해서 들어갔을때의 모습이다.

> 보안상 IP는 편집했다.

![그림4](/assets/img/devops/docker/tomcat/4.png)


이렇게 docker를 활용하여 Tomcat을 운영해봤다.

이번 포스팅은 정말 기본환경만 만든것이고 실제로 서버에서 사용하려면 로컬에 로그를 기록할 **Volume기능**이나, AP단과 톰캣을 하나의 이미지로 묶어 다른서버에서도 사용할 수 있게끔 **Commit 기능**을 사용 할 수 있어야한다.

이는 추후 포스팅에서 다뤄볼 예정 😌
