---
layout: post
title: CI/CD 8. Gitlab과 Jenkins Webhook 연동 및 Maven 빌드
subtitle: Git과 Jenkins Webhook 연동과 빌드 과정 정리
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/devops/cicd/git/0.png
---


## 개요
> Gitlab과 Jenkins의 Webhook을 통한 연동 과정 정리
  
- 목차
	- [`1. Git`](2020-07-12-devops-cicd-install%20helm.markdown)
	- [`2. Git 호스팅서비스`](#2.-Git-호스팅서비스)
	- [`3. 호스팅서비스 선택`](#3.-호스팅서비스-선택)
  
## Git
---
이번 포스팅에서는 Gitlab 설치형서버와 Jenkins서버간 Webhook을 통해 연동하고 Maven으로 빌드하는 과정에 대해 정리한다. 해당 포스팅은 설치형 gitlab과 jenkins, 그리고 Maven으로 빌드할 가벼운 Spring-boot 프로젝트가 사전에 준비되어있음을 전제로 한다.


<br>

> 참고문헌

- Pro Git (프로 Git) 2판

<br>

---

### **1. Jenkins 환경설정**

<br>

> JDK 설정

<br>

Jenkins > Jenkins 관리 > 'Global Tool Configuration' 선택

![그림1](/assets/img/devops/cicd/webhook/2.png)

<br>

![그림1](/assets/img/devops/cicd/webhook/3.png)

<br>

- JDK 1.8 : jenkins가 주로 작동될 환경의 jdk 버전
- JAVA_HOME : 해당 JDK의 경로 (echo $JAVA_HOME 혹은 which java로 확인)
- install automatically 체크박스는 해제(jdk가 서버에 설치되어있다는 전제)

<br>

> git 설정

<br>

git을 Jenkins서버에 설치한경우에는 install automatically 박스를 해제해도 좋다. 필자는 별도로 설치 하지않았기때문에 아래와 같이 체크박스를 선택했다.

<br>

![그림1](/assets/img/devops/cicd/webhook/4.png)

<br>

> Maven 설정

<br>

Add Maven 버튼 클릭후 `Maven 3.6.0` 기입, Install from Apache도 3.6.0으로 버전을 맞춘다. 그리고 install automatically 체크 및 하단의 Save 버튼 클릭

<br>

![그림1](/assets/img/devops/cicd/webhook/5.png)

<br>


> Jenkins의 플러그인 설치

<br>

포스팅했던 [jenkins 설치글](#https://zunoxi.github.io/devops/2020/12/16/infra-linux-jenkins-_install/)을 참고해서 설치했다면 이미 github,gitlab 플러그인은 설치 되어있을 것이다. 설치한적이 없다면 아래를 참고한다.

<br>

- Jenkins 관리 > 플러그인 관리 > 설치가능 탭에서 `git` 검색

<br>

![그림1](/assets/img/devops/cicd/webhook/1.png)



---

### **2. Jenkins 플러그인 설치**

