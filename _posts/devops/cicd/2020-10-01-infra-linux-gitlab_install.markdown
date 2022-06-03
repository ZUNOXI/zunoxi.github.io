---
layout: post
title: CI/CD 4. CentOS7에 Gitlab-ce 설치하기
subtitle: CentOS7에 Gitlab-ce 설치하기
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/infra/linux/gitlab/1.png
---

해당 포스팅은 [https://zunoxi.tistory.com/85?category=950188](https://zunoxi.tistory.com/85?category=950188) 으로 이전되었습니다.

<!--

## 개요
> `Centos7` linux 서버에 `Gitlab 서버`설치하기
  
- 목차
	- [`사전 환경 세팅`](#1-사전-환경-세팅)
	- [`Gitlab-ce 설치`](#2-gitlab-ce-설치)
	- [`계정생성`](#2-gitlab-ce-설치)
  
## Gitlab-ce on Centos7
---
앞선 포스팅에서는 쿠버네티스에서 gitlab 서버를 설치하고 디플로이하는 글을 작성했었다. 이번에는 CI/CD 파이프라인을 사용하지만, 컨테이너환경이 아닌서버에서 환경을 구축하는 경우의 Gitlab을 서버에 설치해보려한다.

<br>

이번 포스팅에서는 테스트용으로 운영중인 **리눅스의 로컬환경(Centos7)에서 Gitlab-ce 서버를 설치**해보려한다.

**참고했던 블로그 포스트**

-   [https://uxgjs.tistory.com/161](https://uxgjs.tistory.com/161)
-   [https://wikidocs.net/16279](https://wikidocs.net/16279)
-   [https://hreeman.tistory.com/m/78?category=597879](https://hreeman.tistory.com/m/78?category=597879)

---

### **1. 사전 환경 세팅**

<br>

**openssh 설치**

```
$ sudo yum install -y curl policycoreutils-python openssh-server openssh-clients
```

**방화벽 해제**

```
$ systemctl stop firewalld
```

**메일서버 설치 **

```
$ sudo yum install postfix
```

> **기존Git 삭제 후 재설치**

(최신버전의 git 유지, 기존 미설치중이면 패스)

```
$ yum remove git
$ yum install http://opensource.wandisco.com/centos/7/git/x86_64/wandisco-git-release-7-1.noarch.rpm
$ yum install git
```

<br>

---

### **2. Gitlab-ce 설치**

이전 포스팅에서도 기술했지만, 필자는 ce 버전을 사용한다. (ee는 기업용이으로 요금폭탄을 맞기 싫기때문에 당연히 ce버전을 사용한다..😌)

```
$ curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

```
$ yum install -y gitlab-ce

```

아래 사진과 같이 설치가 진행되면 정상이다.

<br>

![그림1](/assets/img/infra/linux/gitlab/2.png)

<br>

이제 포트변경을 통해 gitlab 서버에 접근하기 용이하게 변경할 수 있다.

```
$ vi /etc/gitlab/gitlab.rb
```

![그림2](/assets/img/infra/linux/gitlab/3.png)


붉은상자표시로 마킹한 곳에 접속할 도메인주소나 `'http://ip:port'` 형식으로 접근 경로를 기입한다.

또한, 실제 레포지터리가 위치할 디렉터리도 바꿔줄수 있다. 해당방법은 아래 링크를 참고하면 좋을것같다.

[https://uxgjs.tistory.com/191](https://uxgjs.tistory.com/191)

설정을 완료한 이후 설정을 재적용해준다.

```
$ sudo gitlab-ctl reconfigure

```

다음, 방화벽 오픈은 다음과 같이 진행한다.

<br>

```
$ sudo systemctl restart firewalld
$ sudo firewall-cmd --permanent --add-service=http
$ sudo firewall-cmd --add-port=내부 Nginx웹서버 포트(일반적으로 80포트)/tcp --permanent
$ sudo systemctl restart firewalld

```

방화벽 오픈후 `sudo gitlab-ctl start` 명령어를 입력하고 웹브라우저에서 주소창에 설정한 **ip:port나 도메인을 입력**한다.

이때, **`502 에러`** 가 발생할 수 있는데 해당경우는 아래와같이 디렉토리에 권한을 부여해서 해결할 수 있다.

```
$sudo chmod -cR 755 /var/log/gitlab
```
<br><br>

---

### **3. 계정생성**

<br>

여기서 개인적으로 많은 어려움을 겪었던 것이 최초 **관리자계정**으로의 접속이었다.

기존의 `컨테이너환경`에서 Gitlab을 설치하고 사용했을때는 별도의 관리자계정이라는것이 없고, 설치완료 후 Register하여 바로 사용했었었다.

<br>

컨테이너가아닌 일반적인 OS로컬의 Gitlab설치는 Community버전으로 설치해도 admin계정이 존재하는것같다. (컨테이너로 설치했을때도 있는데, 없는것 처럼 느끼고 사용했을 수도 있다...)

무튼 최초 Gitlab설치후 초기 관리자 계정과 암호는 다음과 같다.

> username : root / password : 5iveL!fe

초기 접속화면에서 비밀번호를 변경했다면, admin계정의 비밀번호를 변경한것이니 위 password는 변경한 비밀번호로 입력하면 된다.

<br><br>

이후 일반계정으로 Register 요청한 이후에는 admin계정으로 로그인하고 `Admin Area`의 `Overview-Users`탭으로 이동후, `Pending approval`에서 요청이들어온 계정을 승인해주면 된다.

관련내용은 아래 그림을 참고하면 될 것 같다.


![그림3](/assets/img/infra/linux/gitlab/4.png)

<br>

![그림4](/assets/img/infra/linux/gitlab/5.png)

-->
