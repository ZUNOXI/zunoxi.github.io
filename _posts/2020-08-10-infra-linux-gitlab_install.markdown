---
layout: post
title: Linux 3. CentOS7에 Gitlab-ce 설치하기
subtitle: CentOS7에 Gitlab-ce 설치하기
categories: infra
tags: infra linux/aix
comments: true
published: true
header-img: img/infra/linux/gitlab/1.png
---

## 개요
> `Centos7` linux 서버에 `Gitlab 서버`설치하기
  
- 목차
	- [`사전 환경 세팅`](#1-사전-환경-세팅)
	- [`Gitlab-ce 설치`](#2-gitlab-ce-설치)
  
## Gitlab-ce on Centos7
---
앞선 포스팅에서는 쿠버네티스에서 gitlab 서버를 설치하고 디플로이하는 글을 작성했었다. 다만, 왜...인지는 모르겠지만😢 특정 클러스터내에서는 설치가 잘 되었는데..필자가 별도로 구축한 kubernetes에서는 통신문제로 설치가 안되었었다.

<br>

redis와 gitlab간의 통신오류로 에러가 출려되는데 어떤 부분을 어떻게 핸들링해야하는것인지 알 수가 없었다. 거의 2~3일간 구글링을하다가 해결을 못하고, 결국 개인 테스트 플젝에는 로컬서버 깃랩을 설치 및 이용하기로 했다.

이번 포스팅에서는 테스트용으로 운영중인 **리눅스의 로컬환경(Centos7)에서 Gitlab-ce 서버를 설치**해보려한다.

**참고했던 블로그 포스트**

-   [https://uxgjs.tistory.com/161](https://uxgjs.tistory.com/161)
-   [https://wikidocs.net/16279](https://wikidocs.net/16279)

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

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/gitlab/2.png)

<br>

이제 포트변경을 통해 gitlab 서버에 접근하기 용이하게 변경할 수 있다.

```
$ vi /etc/gitlab/gitlab.rb
```

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/gitlab/3.png)


붉은상자표시로 마킹한 곳에 접속할 도메인주소나 `'http://ip:port'` 형식으로 접근 경로를 기입한다.

또한, 실제 레포지터리가 위치할 디렉터리도 바꿔줄수 있다. 해당방법은 아래 링크를 참고하면 좋을것같다.

[https://uxgjs.tistory.com/191](https://uxgjs.tistory.com/191)

설정을 완료한 이후 설정을 재적용해준다.

```
$ sudo gitlab-ctl reconfigure
$ sudo gitlab-ctl start

```

이후 주소창에 설정한 ip:port나 도메인을 입력한다. 물론 해당 포트에 대한 방화벽 오픈은 필수이다.