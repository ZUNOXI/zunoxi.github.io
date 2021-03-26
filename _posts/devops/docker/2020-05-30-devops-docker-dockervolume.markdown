---
layout: post
title: Docker 7. Volume 활용 Tomcat log 저장
subtitle: Docker Volume 활용해서 Tomcat log 호스트에 저장하기
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요
> `Docker volume` 을 활용하여 `Was log`를 호스트에 저장하기 
  
- 목차
	- [`저장할 폴더 생성`](#1-tomcat의-로그를-저장할-폴더-생성)
	- [`Volume 경로 설정`](#2-volume-경로-설정)
	- [`log 파일 적재 확인`](#3-log-파일-적재-확인)
	- [`로그파일 유지확인`](#4-컨테이너-삭제-시-로그-파일-유지되는-것을-확인)
  
## Docker Volume
---
톰캣을 도커 컨테이너로 운영하게 되면 `방대한 로그의 양` 때문에 컨테이너의 용량이 매우 커지게 된다. Docker의 데이터 경로를 별도로 설정해 주었다고 해도 컨테이너가 삭제되면 로그파일도 함께 날아가기에, `비휘발성 데이터들은 로컬에 저장`하는게 안정적일 것이다. 그런 의미로 이번 포스팅에서는 Docker의 Volume기능을 활용하여 호스트에 데이터를 저장하고 연동해보려 한다.

<br>


### **1\. Tomcat의 로그를 저장할 폴더 생성**

![그림1](/assets/img/devops/docker/log/1.png)

현재 WAS를 두 개 운영 중인 관계로 "tomcat\_test"라는 폴더를 별도로 만들었다. 해당 폴더 내에 log라는 폴더도 생성했다ㄴ. 현재 돌리고 있는 'tomcat\_test'라는 톰캣 컨테이너는 아래처럼 로그가 쌓이고 있다.

<br>

![그림2](/assets/img/devops/docker/log/2.png)

이는 해당 컨테이너가 삭제될 경우 로그도 함께 삭제될 수 있는 위험이 있기 때문에 호스트에 경로를 지정해 줄 것이다.

<br>

---

### **2\. Volume 경로 설정**

Docker에서 `Volume을 설정`해 줄 수 있는 방법은 여러 가지이다. _Dockerfile_ 에서 호스트 경로를 명시해줄 수도 있고, 이미 _실행 중인 컨테이너_ 에서 지정해줄 수도 있으며 최초 Docker이미지를 실행시켜 컨테이너를 생성 시에 정의해 줄 수 도 있다.

이번 포스팅에서는 마지막 방법인 Docker 이미지 최초 실행 시에 호스트 경로를 지정할 것이다. 기존에 실행 중이던 톰캣 컨테이너를 먼저 삭제하고 진행했다.

![그림3](/assets/img/devops/docker/log/3.png)

다음, 아래 명령어를 쉘에 입력한다.

```
docker run -d --name tomcat_test -p 8080:8080 -v /u01/tomcat_test/log:/usr/local/tomcat/logs tomcat
```

명령어에 대한 설명을 덧붙이자면

> docker run -d --name \[만들 컨테이너 명\] -p \[외부포트\]:\[도커 내부포트\] -v \[지정할 호스트 경로\]:\[컨테이너 내 지정 경로\] \[이미지 id 혹은 이름\]

이렇게 이해하면 되겠다.

<br>

---

### **3\. log 파일 적재 확인**

브라우저를 통해 해당 서버의 8080 포트로 접속했을 때 서버 호스트에 지정된 위치에 로그파일이 생기는 것을 확인할 수 있다.

![그림4](/assets/img/devops/docker/log/4.png)

<br>

---

### **4\. 컨테이너 삭제 시 로그 파일 유지되는 것을 확인**

![그림5](/assets/img/devops/docker/log/5.png)

`tomcat\_test` 컨테이너를 삭제해도 로그가 지정된 폴더에 그대로 남아있는 것을 확인할 수 있다.

다음 포스팅에서는 컨테이너 내에서 변경했던 설정 기준으로 다시 이미지화시키는 Commit에 대해 알아볼 예정 😌