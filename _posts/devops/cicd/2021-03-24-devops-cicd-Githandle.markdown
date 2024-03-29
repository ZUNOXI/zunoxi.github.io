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

해당 포스팅은 [https://zunoxi.tistory.com/101?category=950188](https://zunoxi.tistory.com/101?category=950188) 으로 이전되었습니다.

<!--

## 개요
> `Git`을 활용을 위한 Git 호스팅 서비스 정리
  
- 목차
	- [`1. Git`](#1.-Git)
	- [`2. Git 호스팅서비스`](#2.-Git-호스팅서비스)
	- [`3. 호스팅서비스 선택`](#3.-호스팅서비스-선택)
  
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

이처럼 Git은 로컬버전 관리인 VCS 도구들과 중앙집중식 버전 관리(CVCS), 분산 버전 관리 시스템들의 장단점이 적절히 섞인 시스템인데, 일반적으로 우리 사용자들은 이러한 `Git을 호스팅`하는 사이트를 이용하여 리모트 서버 혹은 저장소에서 소스를 저장 및 공유하게 된다. 


<br>

---

### **2. Git 호스팅서비스**

<br>

이번 포스팅에서는 Git 호스팅 서비스로 주로 이용되는 `Github, Gitlab, bitbucket`에 대한 특징(장단점)을 주로 다뤄본다. 또한, 호스팅서비스는 서비스가 변하고 있는 업계라 그 기준 시간이 언제냐에 따라 정책과 특징이 다르다. 해당 포스팅을 작성하는 시점은 _**2021.03.24**_ 이니 참고하면 좋을 것 같다.

<br>

> Github

<br>

![그림2](/assets/img/devops/cicd/git/1.png)


장점

-  git 저장소 호스팅 사이트중 가장 큰 규모의 서비스
-  업로드 / 다운로드의 속도가 가장빠름
-  private repo 무료 사용(협업자 제한없음)
-  최근 무료서비스 정책으로 대부분의 기능 이용가능
-  가장안정적인 서버 제공

<br>

단점

- LFS(Large File Storage)의 트래픽 제한이있다.

<br>

> Gitlab

<br>

![그림3](/assets/img/devops/cicd/git/2.png)


장점
- 설치형 서버제공으로 자체 형상관리 서버운영가능 (오픈소스로 시스템 수정가능)
- private repo 무료사용(협업자 제한 없음)
- Devops pipeline 구축에 최적화(GCP를 이용하여 K8S까지 배포가 용이함)

<br>

단점
- push/pull의 속도가 느림
- 가끔 불안정한 서버상태


<br>

> Bitbucket

<br>

![그림4](/assets/img/devops/cicd/git/3.png)

<br>

장점
- 아틀라시안의 서비스(jira)와 연동편의성이 높다.
- private repo 무료사용
- 직관적인 GUI 제공

단점
- 저장소 참여하는 인원기준으로 과금이 유발됨(5명 이상부터는 과금 부여)


<br>

---

### **3. 호스팅서비스 선택**

<br>

참고로 필자는 본 블로그 호스팅을위해 `github`에서 웹페이지를 호스팅 제공받아 운영하고 있다. 관련내용은 [링크](https://zunoxi.github.io/life/2020/08/26/life-etc-blog/)를 참고하면 좋을 것 같고, 추가로 github은 약 월 4달러씩 지불하며 `Pro 버전`을 사용하고 있다. 

Github무료 버전과 큰 차이는 안나지만 본 블로그를 private repo로 운영하며 호스팅을 할 수 있다는 점이 가장 메리트 있어 나름 거금(?)을 투자중이다. (블로그를 통째로 퍼가는건 싫어서...ㅎ)

<br>

또한, 사내에서는 `Gitlab`을 이용하여 버전관리를 하고있다. 주 고객사가 이제 막 CICD도입을 검토하고 Pilot을 진행하는 단계인데, 사내 소스코드 버전관리는 그룹망에서 접근(외부망 오픈 불필요)과 같은 보안적이유로 설치형 Gitlab 사용을 결정했다.

<br>

이처럼 개인과 조직의 상황을 충분히 고려하여 판단한다면 위 호스팅서비스 중 본인에게 최적의 서비스를 선택할 수 있을것이라 생각한다.



-->
