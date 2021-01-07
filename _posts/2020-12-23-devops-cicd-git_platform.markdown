---
layout: post
title: CI/CD 3. github과 gitlab, 그리고 Bitbucket
subtitle: git을 사용하는 플랫폼들간의 비교
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/devops/cicd/git/git.png
---

## 개요
> 형상관리, 협업을 위한 `git`, 그리고 이를 이용한 플랫폼간의 비교
  
- 목차
	- [`Gitlab on k8s`](#h2-idgitlab-on-k8s-342gitlab-on-k8sh2)
	- [`pv 선언`](#1-persistent-volume퍼시스턴트-볼륨-선언)
	- [`gitlab 설치`](#2-gitlab-설치)
	- [`설치확인`](#3-정상-설치-확인)
  
## Git 저장소
---
Git은 개인 그리고 조직의 소스코드의 `버전관리(형상관리), 추적, 협업`을 위해 핫하게 쓰이고있는 오픈소스이다. 일반적으로 우리는 github, gitlab등 웹호스팅 사이트를 통해 git을 사용하고 있는데, 이번 포스팅에서는 이런 **`git호스팅 서비스들간의 비교`** 를 통해 

<br><br>

앞서 언급한것 처럼 git이라는 오픈소스를 이용한 서비스들이기 때문에 각 기능은 크게 차이나지 않는다. 다만 `과금요소`에 따라 그 기능이 제한되고 또한, `가격정책은 시간에 따라 변할 수 있으므로` **해당 포스팅이 작성된 일자(_2020.12.23_ )를 반드시 확인할 필요**가있다.

<br>

**참고했던 블로그 포스트**

-   [https://m.blog.naver.com/dlwhdgur20/221006619001](https://m.blog.naver.com/dlwhdgur20/221006619001)


<br><br>


### **1. git호스팅 서비스**
---

git호스팅 서비스로 많이사용하는 3개 서비스는 **`Github, Gitlab, Bitbucket`** 이다. 이들은 기본적으로 git의 기능을 지원하며 `원격저장소`를 제공하고 `웹`에서 이를 간편하게 관리하고 운영할 수 있게해준다. 다른 서비스들도 있지만 이번 포스팅에서는 언급한 3개의 서비스들에 대해서만 알아본다.


<br><br>


### **2. Github**
---

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/cicd/git/1.png)

<br>

먼저 Github(이하 깃허브)는 git호스팅중의 선두주자이자 가장 각광받는 서비스이다. 깃허브는 다음과 같은가격정책을 갖고 있다.

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/cicd/git/4.png)

<br>