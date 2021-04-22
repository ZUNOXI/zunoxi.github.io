---
layout: post
title: CI/CD 6. Git과 Git 호스팅 서비스에 대한 정리
subtitle: Git활용을 위한 Git 호스팅 서비스 정리
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/devops/cicd/git/0.png
---

## 개요
> `Git`을 활용을 위한 Git 호스팅 서비스 정리
  
- 목차
	- [`Gitlab on k8s`](#h2-idgitlab-on-k8s-342gitlab-on-k8sh2)
	- [`pv 선언`](#1-persistent-volume퍼시스턴트-볼륨-선언)
	- [`gitlab 설치`](#2-gitlab-설치)
	- [`설치확인`](#3-정상-설치-확인)
  
## Git과 호스팅 서비스
---
앞선 CI/CD 관련 포스팅들에서 Legacy 방식 혹은 컨테이너환경에서의 Gitlab설치를 다뤘었다. 본 포스팅에서는 Git이 무엇인지, 또 Gitlab과 같은 Git 호스팅 서비스 종류들이 어떻게되고 어떤특징이 있는지 정리하려한다.

<br>

> 참고문헌

- Pro Git (프로 Git) 2판

<br>

---

### **1. Git**

<br>

Git은 소스코드 분산버전관리 시스템으로 형상관리 도구라고도 불린다. 일반적으로 버전 관리란 파일의 변화를 시간에 따라 기록하고 추후 특정 시점의 버전을 다시 꺼내올 수 있다는 특징이 있다. 

<br>

![그림1](/assets/img/devops/cicd/git/git.png)

<br>

Git과 유사한 기존의 `CVS, Subversion, Perforce, Bazzar `등의 시스템은 각 파일의 변화를 시간순으로 관리하면서 파일들의 집합을 관리한다.

Git은 이런식으로 데이터를 저장하지도 취급하지도 않는다. Git은 데이터를 파일 시스템 스냅샷으로 취급하고 그 크기가 아주 작다. 파일이 달라지지 않았으면 Git은 성능을 위해서 파일을 새로 저장하지 않는다. 단지 **이전 상태의 파일에 대한 링크만 저장한다.**

 
<br>

이처럼 Git은 로컬버전 관리인 VCS 도구들과 중앙집중식 버전 관리(CVCS), 분산 버전 관리 시스템들의 장단점이 적절히 섞인 시스템인데, 일반적으로 우리 사용자들은 이러한 Git을 호스팅하는 사이트를 이용하여 리모트 저장소를 관리한다. 


<br>

---

### **1. Github**


... 작성중





