---
layout: post
title: Linux 7. CentOS7에 Jenkins 설치하기
subtitle: CentOS7에 Jenkins 설치하기
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


