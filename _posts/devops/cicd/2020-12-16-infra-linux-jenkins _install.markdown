---
layout: post
title: CI/CD 5. CentOS7에 Jenkins 설치하기
subtitle: CentOS7에 Jenkins 설치하기
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/infra/linux/jenkins/jenkins.jpg
---

해당 포스팅은 [https://zunoxi.tistory.com/96?category=950188](https://zunoxi.tistory.com/96?category=950188) 으로 이전되었습니다.
<!--

## 개요
> `Centos7` linux 서버에 `Jenkins`설치하기
  
- 목차
	- [`1. Jenkins 설치`](#1-jenkins-설치)
	- [`2. Jenkins 구성수정`](#2-jenkins-구성수정)
	- [`3. Jenkins 시작 및 접속`](#3-jenkins-시작-및-접속)
	- [`4. Jenkins Plug-in 설치`](#4-jenkins-plug-in-설치)
  
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

![그림1](/assets/img/infra/linux/jenkins/1.png)

<br>

초기비밀번호를 확인해야하니 화면에 나온처럼 경로의 파일을 확인 후 빈칸에 입력한다.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```


<br>

---
### **4. Jenkins Plug-in 설치**


초기 비밀번호를 입력하면 `플러그인`을 설치할 수 있는 화면이 나온다. 이때 'Select plugins to install' 을 클릭하여 직접 플러그인을 설치한다.

다음 포스팅에 몇가지 플러그인을 더 설치할 예정으로 최초 설치시에는 github, gitlab만 추가로 더 선택하여 설치 해보도록한다. 

<br>

기존 선택된 플러그인에서 github, gitlab만 선택 후 설치를 진행하면 아래와 같은 화면이 나온다.

![그림2](/assets/img/infra/linux/jenkins/2.png)

<br>

---
### **5. 계정정보 입력 및 도메인 확인**

<br>

해당 화면에서는 젠킨스에서 사용할 계정명과 암호를 입력한다. 여러번 설치하고 사용하며 느낀것은 계정명을 자주 까먹는(?) 경우가 있어서, 필자는 admin으로 설정했다.. 


![그림3](/assets/img/infra/linux/jenkins/3.png)

<br>

다음 본인이 사용할 주소와 포트 혹은 도메인이 정상적으로 입력이 되어있는지 확인한다.

<br>

![그림4](/assets/img/infra/linux/jenkins/4.png)

<br>

최초 설치과정 완료 :)

![그림5](/assets/img/infra/linux/jenkins/5.png)

<br>

이렇게 메인페이지까지 접속이 되었다면 설치가 완료된것이다.

<br>

![그림6](/assets/img/infra/linux/jenkins/6.png)


이제 gitlab과 jenkins를 이용해서 CICD 테스트의 일환으로 `서버에 파일을 배포`해보는 테스트를 진행해볼 예정이다 :)


