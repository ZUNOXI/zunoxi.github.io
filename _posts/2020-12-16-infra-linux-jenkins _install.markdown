---
layout: post
title: Linux 7. CentOS7에 Jenkins 설치하기
subtitle: CentOS7에 Jenkins 설치하기
categories: infra
tags: infra linux/aix
comments: true
published: true
header-img: img/infra/linux/jenkins/jenkins.jpg
---

## 개요
> `Centos7` linux 서버에 `Jenkins`설치하기
  
- 목차
	- [`1. Jenkins 설치`](#1-jenkins-설치)
	- [`2. Jenkins 구성수정`](#2-jenkins-구성수정)
	- [`3. Jenkins 시작 및 접속`](#3-jenkins-시작-및-접속)
  
## Jenkins on Centos7
---
지난 포스팅에서 CI/CD 파이프라인 구축시, 컨테이너환경이 아닌서버에서 환경을 구축하는 경우의 Gitlab설치를 다뤄봤다. 이번에는 해당서버에 Jenkins까지 구축하고 본격적인 CI/CD기반을 만들어본다.

<br>

이번 포스팅에서는 테스트용으로 운영중인 **리눅스의 로컬환경(Centos7)에서 Gitlab-ce 서버를 설치**해보려한다.

**참고했던 블로그 포스트**

-   [https://blog.jiniworld.me/88](https://blog.jiniworld.me/88)

---

### **1. Jenkins 설치**

Jenkins를 설치이전 **`Public Key를 설정`** 해서 yum에서 패키지를 다운받을 수 있게 한다.

```
$ sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
$ sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
```

다음, Jenkins를 설치한다.

```
sudo yum install jenkins
```

<br>

---
### **2. Jenkins 구성수정**

Jenkins의 `기본포트는 8080`으로 설정이 되어있다. 일반적으로 톰캣같은 WAS가 8080으로 포트를 사용하고 있기에 겹칠수 있으므로 포트를 변경해준다. 필자같은 경우는 `7009포트로 변경`했다.

```
$ sudo vi /etc/sysconfig/jenkins
.
.
JENKINS_PORT="7009"  # 해당파일의 JENKINS_PORT="8080"을 수정
.
.
```

다음, 너무나도 당연하지만 변경한 Jenkins의 포트에대해 `방화벽오픈`해준다.

```
firewall-cmd --permanent --add-port=7009/tcp
systemctl restart firewalld
```

<br>

---
### **3. Jenkins 시작 및 접속**

방화벽오픈까지 완료되면 `sudo systemctl start jenkins`를 입력해준다.

그리고 브라우저의 `http://ip:port` 혹은 `http://도메인`을 입력한다.

아래 사진처럼 나왔다면 정상적으로 설치가 완료된것이다.

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/jenkins/1.png)

<br>

사실 CICD or Devops에 더가까운 포스팅이지만, 컨테이너환경에서의 설치가 아니라 infra분류로 포스팅해봤다. 이제 gitlab과 jenkins를 이용해서 CICD 테스트에 대한 포스팅을 해볼예정이다.



