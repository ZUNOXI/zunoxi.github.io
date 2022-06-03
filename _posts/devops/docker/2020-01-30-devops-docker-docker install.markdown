---
layout: post
title: Docker#2.윈도우/리눅스에 도커(Docker) 설치
subtitle: 도커 윈도우/리눅스 설치
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

해당 포스팅은 [https://zunoxi.tistory.com/25?category=950190](https://zunoxi.tistory.com/25?category=950190)으로 이전하였습니다.
<!--

이번 포스팅에서는 서버에 띄우기 위한 도커파일을 만들기 위해 윈도우와 리눅스에서 도커를 설치해 보려한다.

리눅스가 개발환경인 경우의 설치방법은 생각보다 간편하다. 물론, 테스트 서버에서도 docker 이미지 파일이 돌아가게 해야하므로 테스트 목적의 서버 환경에도 도커를 설치해야한다.

AWS 접속과 관련된 배치파일 생성하는것은 아래 포스팅을 참고

[https://zunoxi.github.io/cloud/2020/01/31/cloud-aws-aws-access/](https://zunoxi.github.io/cloud/2020/01/31/cloud-aws-aws-access/)


---


**(1) Window에 Docker 설치**

먼저 윈도우 설치를 알아보려한다.

1. 가상화 기능 지원 확인 

자, 먼저 본인의 윈도우가 가상환경을 지원하는지 부터 확인해 봐야한다. 윈도우 10버전은 아래와 같이 작업관리자의 가상화 지원여부에서 확인 가능하다.

![그림1](/assets/img/devops/docker/install/1.png)
<br><br>

**2\. Docker 다운로드**

윈도우 7과 윈도우 10 홈 버전 이하는 아래의 링크를 다운로드 하시면 된다.

[https://docs.docker.com/toolbox/toolbox\_install\_windows/](https://docs.docker.com/toolbox/toolbox_install_windows/)


그 이상의 버전(윈도우 10 엔터프라이즈, 프로 등)은 아래 링크에서 다운로드.

[https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows](https://docs.docker.com/docker-for-windows/install/#download-docker-for-windows)
<br><br>

**3\. Docker 설치**

필자가 쓰고있는 지금 환경은 Window 10 enterprise 이기때문에 **docker for window**로 설치를 진행했다. 그 외의 버전은 아래 링크의 방법을 참고 :)

[https://steemit.com/kr/@mystarlight/docker](https://steemit.com/kr/@mystarlight/docker)



본격적인 docker 설치 전, 프로그램 및 기능에서 **Hyper-V**를 설치한다.

![그림2](/assets/img/devops/docker/install/2.png)

![그림3](/assets/img/devops/docker/install/3.png)


설치 후 재부팅 해주면, 로그인 하라고 상태창이 오픈된다. 로그인을 해주고...

![그림4](/assets/img/devops/docker/install/4.png)


작업 표시줄에 고래모양 아이콘이 생긴다. 잘 설치가 된것이다 :) cmd 창에 - **docker version** 을 입력함으로서 확실히 확인해본다. 환경변수로 도커가 등록이 되어 있는지 확인하는 과정이다.

![그림5](/assets/img/devops/docker/install/5.png)

자 이제 윈도우에 docker가 설치가 완료 되었다. 다음은 리눅스에 docker를 설치하는 과정이다.

---
<br><br>

**(2) LINUX(ubuntu)에 Docker 설치**

필자가 본 포스팅에서 사용하는 서버OS는 ubuntu 18.04.3 LTS이며 AWS EC2를 이용중 이다. 온프레미스 환경에서 서버를 운영한다면 물리서버에 RHEL이나 CentOS를 운영하는 환경이 많을테지만, 현재는 작은 프로그램 개발 테스트 중이라 AWS의 힘을 빌리고 있다.

AWS를 사용하여 docker를 설치 시 CLI 화면에서 작업할것이므로 명령어 위주로 기술 하려한다.
<br><br>

> **1\. 환경 세팅**

먼저 docker 설치에 앞서 설치에 필요한 패키지들을 설치한다.

```
$ sudo apt-get update && sudo apt-get install 
	\ apt-transport-https 
        \ ca-certificates 
        \ curl 
        \ software-properties-common
```

다음 패키지 저장소를 추가한다.

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

이제 아래의 명령어로 docker의 패키지가 검색이 되는지 확인.

```
sudo apt-get update && sudo apt-cache search docker-ce
```

**docker-ce - Docker: the open-source application container engine** 라는 결과가 출력되면 설치를 진행한다.

**설치가 잘안된다면 아래의 방법으로 해보길 권장**

```
sudo apt-get update
sudo apt install curl


curl -fsSL https://get.docker.com/ | sudo sh


sudo usermod -aG docker $USER # 현재 접속중인 사용자에게 권한주기
sudo usermod -aG docker 사용자 이름 # 사용자에게 권한주기

```
<br><br>

> **2\. Docker 설치**

아래의 명령어를 이용하여 Docker CE 에디션을 설치해 보겠다.

```
$ sudo apt-get update && sudo apt-get install docker-ce
```

현재 상태로는 sudo로 명령어를 입력할 시에만 docker를 접근할 수 있습다. 일반 ubuntu사용자도 접근이 가능하게 끔 아래의 명령어를 한번 더 입력해 주겠다.

```
$ sudo usermod -aG docker $USER
```

![그림6](/assets/img/devops/docker/install/6.png)

서버상에도 정상적으로 Docker 가 설치된것을 알 수 있다.

**\* CentOS에서의 설치는 매우 간단하다. (아래 내용 참고, CentOS7.x 기준)**

```
# Docker & Docker Registry 설치
yum -y install docker docker-registry

#부팅시 실행하도록 등록
systemctl enable docker.service

# Docker 실행
systemctl start docker.service

# docker 스테이터스 확인
systemctl status docker.service

## 설치 후 재시작
reboot
```

(nvdia-docker 설치는 [https://www.leafcats.com/153](https://www.leafcats.com/153) 포스팅을 참고)

**자, 이렇게 Docker를 윈도우, 리눅스 환경에 모두 설치 완료 했다.** **다음 포스팅에서는 본격적으로 Dockerfile을 만들고 이미지를 생성하는것까지 진행 할 예정.**
