---
layout: post
title: Docker Volume 활용 Tomcat log 저장
subtitle: Docker Volume 활용해서 Tomcat log 호스트에 저장하기
categories: infra
tags: infra docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요

---

톰캣을 도커 컨테이너로 운영하게 되면 방대한 로그의 양 때문에 컨테이너의 용량이 매우 커지게 된다.

 

Docker의 데이터 경로를 별도로 설정해 주었다고 해도 컨테이너가 삭제되면 로그파일도 함께 날아가기에,

 

비휘발성 데이터들은 로컬에 저장하는게 안정적일 것이다. 

 

그런 의미로 이번 포스팅에서는 Docker의 Volume기능을 활용하여 호스트에 데이터를 저장하고 연동해보려 한다.



본 내용은 [`티스토리 포스팅을 참고`](https://zunoxi.tistory.com/50)