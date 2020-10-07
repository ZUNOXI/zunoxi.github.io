---
layout: post
title: 알아두면 편리한 docker command
subtitle: docker command 정리
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요
> `Git push` 부터 `쿠버네티스` 파드 배포 까지 파이프라인 구축 &#10102;단계
  
- 목차
	- [`DevOps구조`](#devops구조)
	- [`Helm?`](#helm)
	- [`Why Helm`](#why-helm-)
	- [`How to install`](#how-to-install-)
  
## Helm??
---
헬름은 쿠버네티스 차트를 관리하기 위한 도구이다. 차트는 사전 구성된 쿠버네티스 리소스의 패키지라고 할 수 있다. 즉, 쿠버네티스 패키지 관리 도구이다. `Helm 설치로 필요한 프로그램 패키지 파드 배포`


## 개요
> docker와 그 container를 다룰때 편리한 `command`들을 정리
  
- 목차
	- [`컨테이너 생성 및 시작`](#1-컨테이너-생성-및-시작)
	- [`컨테이너 가동 확인?`](#2-컨테이너-가동-확인)
	- [`컨테이너 정지 및 재시작`](#3-컨테이너-정지-및-재시작)
	- [`컨테이너 삭제`](#4-컨테이너-삭제)

## Dokcer Command
---
컨테이너환경에서 개발을 하다보면 도커의 명령을 매우 자주 사용하게 된다. 이부분은 인프라엔지니어가 아니라도 일반 개발자도 `Devops가 적용된 개발환경`이라면 도커에 대한 명령어를 자주 쓰게되므로 해당 글을 정리한다.

<br>

### 1. 컨테이너 생성 및 시작

> docker container run [옵션] 이미지명[:태그명] [인수]

위 명령어는 이미지를 이용해 컨테이너를 생성함과 동시에 시작한다.

- 옵션

* `-a, -attach` : 표준 입력, 표준 출력, 표준 오류 출력을 어태치 한다.
* `-d, -detach` : 컨테이너를 생성하고 백그라운드에서 실행
* `-p, -publish` : 호스트의 임의의 포트를 컨테이너에 할당
* `--name` : 컨테이너의 이름을 지정
* `-v, --volume` : 호스트와 컨테이너의 디렉토리를 공유 ([`링크`](https://zunoxi.tistory.com/50?category=871486)참조)


아래 명령어는 기존에 만들어져있던 컨테이너를 구동시킨다.

> docker container start [옵션] [컨테이너 식별자]

<br>

### 2. 컨테이너 가동 확인

> docker container stats [컨테이너 식별자]


### 3. 컨테이너 정지 및 재시작

<br>

#### (1). 컨테이너 정지

> docker container stop [옵션] [컨테이너 식별자]

#### (2). 컨테이너 재시작

> docker container restart [옵션] [컨테이너 식별자]

<br>

### 4. 컨테이너 삭제


#### (1). 특정 컨테이너 삭제

> docker container rm [옵션] [컨테이너 식별자]

`옵션`

* -f, --force : 실행 중인 컨테이너를 강제로 삭제


#### (2). 정지중인 모든 컨테이너 삭제

> docker container prune 

<br>

### 5. 컨테이너 설정확인

> docker container inspect [컨테이너 식별자]

<br>

### 6. 컨테이너 조회

> docker container ls -a

<br>

### 참조

- 도서 [`완벽한 IT인프라 구축을 위한 Docker`](http://www.yes24.com/Product/Goods/64728692)