---
layout: post
title: CI/CD 4. github과 gitlab, 그리고 Bitbucket
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
	- [`Github`](#2-github)
	- [`Gitlab`](#3-gitlab)
	- [`Bitbucket`](#4-bitbucket비트버킷)
	- [`비교정리`](#5-정리)
  
## Git 저장소
---
Git은 개인 그리고 조직의 소스코드의 `버전관리(형상관리), 추적, 협업`을 위해 핫하게 쓰이고있는 오픈소스이다. 일반적으로 우리는 github, gitlab등 웹호스팅 사이트를 통해 git을 사용하고 있는데, 이번 포스팅에서는 이런 **`git호스팅 서비스들간의 비교`** 를 통해 장단점을 정리한다.

<br>

앞서 언급한것 처럼 git이라는 오픈소스를 이용한 서비스들이기 때문에 각 기능은 크게 차이나지 않는다. 다만 `과금요소`에 따라 그 기능이 제한되고 또한, `가격정책은 시간에 따라 변할 수 있으므로` **해당 포스팅이 작성된 일자(_2020.12.23_ )를 반드시 확인할 필요**가있다.

<br>

**참고했던 블로그 포스트**

-   [https://m.blog.naver.com/dlwhdgur20/221006619001](https://m.blog.naver.com/dlwhdgur20/221006619001)
-   [https://bitlog.tistory.com/158](https://bitlog.tistory.com/158)


<br><br>

---
### **1. Git호스팅 서비스**


일반적으로 git호스팅 서비스로 많이 언급되는것은 **`Github, Gitlab, Bitbucket`** 이다. 이들은 기본적으로 git의 기능을 지원하며 `원격저장소`를 제공하고 `웹`에서 이를 간편하게 관리하고 운영할 수 있게해준다. 다른 서비스들도 있지만 이번 포스팅에서는 언급한 3개의 서비스들에 대해서만 정리해봤다.


<br><br>

---
### **2. Github**

<br>

![그림1](/assets/img/devops/cicd/git/1.png)

<br>

먼저 **`Github`** 는 git호스팅중의 선두주자이자 가장 각광받는 서비스이다.
<br>

다른 플랫폼도 마찬가지지만 github은 서비스시작이후부터 _**가격정책의 변동**_ 이 여러번있었다. 특히, 2018년말 `MS`의 `github 인수`가 완료되면서 업계는 오픈소스의 성지인 github인수에 대해 많은 우려를 가졌지만, 결과적으로 **`가격정책은 상당히 혜자스러워졌다.`**
<br>

눈에띄는 변화로는 기존에 개인이 `비공개 저장소(Private Repository)`를 이용하려면 과금을 통해서만 사용할수 있었지만, `2019년 1월`이후 깃헙은 블로그를 통해 무료플랜에서도 비공개저장소를 무제한으로 만들어 사용할 수 있다고 발표했다. 단, **협업은 3명까지만 가능**하고 그 이상은 과금을 해야했다. 

> [참고 : https://github.blog/2019-01-07-new-year-new-github/](https://github.blog/2019-01-07-new-year-new-github/)


이후, 2020년 4월부터 비공개 저장소에대한 협업가능한 공동작업자수제한도 풀리며 사실상 **`일반적으로 사용하는 기능은 전면 무료화`** 가되었다.

<br>

깃헙은 현재(2020.12.23 기준) 다음과 같은가격정책을 유지하고있다.

<br>

![그림2](/assets/img/devops/cicd/git/4.png)

[링크 : https://github.com/pricing](https://github.com/pricing)

<br>

위 사진은 `teams private repository`에 대한 가격정책이다. 무료플랜도 2000분의 github action과 500mb의 github package 스토리지를 제공하기에 일반적으로 사용하기에는 무리가 없을것으로 판단된다. 위용어들은 [참고포스팅](https://bitlog.tistory.com/158)을 확인하면 좋을 것 같다.

<br>

개인계정같은 경우 free에서 `pro`로 전환(`4$/월`)하게되면 github page를 private하게 운영할 수 있다는게 장점인것 같다. 현재 이 블로그도 github에서 호스팅해주는 github page인데 free plan에서는 github page를 사용하는 저장소(repo)는 public으로밖에 사용할 수 없다. 

즉, 누군가 마음만 먹으면 본 블로그 repo를 그대로 fork떠서 내용을 조금 수정하고 본인의 블로그 처럼 사용할 수 있다는 뜻이다. `그게 뭐가 문제냐??` 하면 물론 이는 오픈소스의 순기능으로 문제될것이 전혀없지만, 필자의 경우 블로그 곳곳 노출되지 않는 소스에 프라이빗한 정보가 있기때문에 이런기능을 차단하고 싶었다. 

그런의미에서 pro plan은 꽤 매력이있으며 별도로 블로그를 구축하고 자체 호스팅하기전까지는 pro를 사용할 것 같다. 필자처럼 **본인에게 해당 과금이 필요한 요소인지 판단**하고 구매하는것이 좋을 것 같다.


<br><br>

---

### **3. Gitlab**


<br>

![그림3](/assets/img/devops/cicd/git/2.png)

<br>

gitlab은 github의 후발주자로 그 기능은 github과 유사하므로 장단점 위주로 비교를해보려한다. gitlab같은경우는 `Devops에 최적화`된 서비스를 제공하고, `설치형`으로 gitlab 서버자체를 운영할 수 있다는 장점이 있다. 

기존에는 비공개 저장소를 무료로 이용할 수 있다는 장점도 있었지만, 현재 github도 이기능을 지원하고 있기에 이부분은 큰메리트로 다가오지 않는것 같다.

단점으로는 원격저장소까지 `전송속도(push, pull 등)가 github보다 느리고 서버가 불안정`([참고](https://about.gitlab.com/blog/2017/02/01/gitlab-dot-com-database-incident/))할때가 있다.

<br>

개인적으로 gitlab같은경우에는 `사내에 특정 서버에 설치형으로 gitlab을 이용`하여 내부망에서 형상관리서비스를 이용할때 가장 경쟁력있다고 생각한다. github이 비공개저장소가 무료라해도 결국은 외부망이 오픈되어있어야 접근할 수 있기때문에 `인트라넷을 사용하는 사내`에서는 설치형 gitlab을 사용하는것이 합리적이다.


<br><br>

---

### **4. Bitbucket(비트버킷)**


<br>

![그림4](/assets/img/devops/cicd/git/3.png)

<br>

비트버켓같은경우 필자가 사용해본적이 없어서 자세히는 모르지만, 개발사가 `지라(Jira)와 소스트리(Sourcetree)`를 개발한 아틀라시안(Atlassian)이다. 

상당히 깔끔한 GUI를 제공하는것으로 유명한 개발사이므로 `GUI가 직관적`이고 `아틀라시안이 개발한 툴들과 연동`이 편하다는 장점이 있다.

또한, 비트버킷은 AWS와의 통합기능을 제공한다.` AWS 코드 디플로이(CodeDeploy)`를 활용하여 EC2에서 코드를 실행할 수 있다고 한다.


단, 유료버전을 사용하지 않으면 `5명이하의 사용자만 협업 작업이 가능`하다.

<br><br>

---

### **5. 정리**


<br>

앞서 언급한 내용들을 기반으로 정리하자면 다음과 같다.

> Github

- 장점
  - git 저장소 호스팅사이트중 가장큰 규모의 서비스
  - 업로드/다운로드의 속도가 가장빠름
  - private repo 무료사용(협업자 제한없음)
  - 최근 무료서비스 정책으로 일반적으로 사용할때, github이 제공하는 대부분의 기능 이용가능 
  - 가장 안정적인 서버 제공
- 단점
  - LFS(Large File Storage)의 트래픽제한이있다.
   
> Gitlab

- 장점
  - 설치형 서버로 자체 형상관리 서버운영가능
  (오픈소스로 시스템수정가능)
  - private repo 무료사용(협업자 제한없음)
  -	Devops pipeline 구축에 최적화(GCP를 이용하여 K8S까지 배포가 용이함)
- 단점
  - push/pull의 속도가 느림
  - 가끔 불안정한 서버상태

> Bitbucket

- 장점
  - 아틀라시안의 서비스(jira)와 연동편의성이 높다.
  - private repo 무료사용
  - 직관적인 GUI
- 단점
  - 저장소 참여하는 인원기준으로 과금이 유발됨
  (5명 이상부터 과금 부여)


<br><br>

위 내용들을 참고하여 실제 운영시 본인과 조직의 환경에 가장 적합한 호스팅사를 선택하여 사용하는것이 좋을것 같다 :)