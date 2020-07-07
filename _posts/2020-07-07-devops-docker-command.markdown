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
---
> docker와 그 container를 다룰때 편리한 command들을 정리

<p>


</p>

### Dokcer Command

---

### 1. 컨테이너 생성 및 시작

> docker container run [옵션] 이미지명[:태그명] [인수]

위 명령어는 이미지를 이용해 컨테이너를 생성함과 동시에 시작한다.

`옵션`

* -a, -attach : 표준 입력, 표준 출력, 표준 오류 출력을 어태치 한다.
* -d, -detach : 컨테이너를 생성하고 백그라운드에서 실행
* -p, -publish : 호스트의 임의의 포트를 컨테이너에 할당
* --name : 컨테이너의 이름을 지정
* -v, --volume : 호스트와 컨테이너의 디렉토리를 공유 ([`링크`](https://zunoxi.tistory.com/50?category=871486)참조)


아래 명령어는 기존에 만들어져있던 컨테이너를 구동시킨다.

> docker container start [옵션] [컨테이너 식별자]



### 2. 컨테이너 가동 확인

> docker container stats [컨테이너 식별자]


### 3. 컨테이너 정지 및 재시작


#### (1). 컨테이너 정지

> docker container stop [옵션] [컨테이너 식별자]

#### (2). 컨테이너 재시작

> docker container restart [옵션] [컨테이너 식별자]






### 참조

- 도서 [`완벽한 IT인프라 구축을 위한 Docker`](http://www.yes24.com/Product/Goods/64728692)