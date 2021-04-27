---
layout: post
title: CI/CD 8. Git과 Jenkins Webhook 연동
subtitle: Git과 Jenkins Webhook 연동 과정 정리
categories: devops
tags: devops CICD
comments: true
published: true
header-img: img/devops/cicd/git/0.png
---

## 개요
> Git과 Jenkins의 Webhook을 통한 연동 과정 정리
  
- 목차
	- [`1. Git`](#1.-Git)
	- [`2. Git 호스팅서비스`](#2.-Git-호스팅서비스)
	- [`3. 호스팅서비스 선택`](#3.-호스팅서비스-선택)
  
## Git
---
이번 포스팅에서는 Gitlab 설치형서버와 Jenkins서버간 Webhook을 통해 연동단계를 구현하는 과정에 대해 정리한다.


<br>

> 참고문헌

- Pro Git (프로 Git) 2판

<br>

---

### **1. Git**

