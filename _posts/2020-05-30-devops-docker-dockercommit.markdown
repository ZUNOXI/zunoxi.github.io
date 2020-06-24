---
layout: post
title: Docker Commit 활용 컨테이너 이미지화
subtitle: Docker Commit 활용해서 컨테이너를 이미지화시키기
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요

---

Docker 사용 시 가끔 컨테이너 내부에서 프로그램의 환경을 수정하는 경우가 생기는데,

**도커이미지는 읽기 전용**이기 때문에 컨테이너에 수정된 설정데이터가 쌓이게 된다.

이는 즉, 수정된 해당 컨테이너는 컨테이너 삭제시 변경했던 데이터가 모두 초기화 되는것을 의미한다.

예를 들어 지난 포스팅에서 다뤘던 tomcat이나 아파치 웹서버를 도커로 운영 할 시, 

가상호스트 설정 혹은 웹프로그래밍 파일을 컨테이너 내에 적재한 상태에서 해당 컨테이너가 삭제될 때

**다시 이미지를 실행시키고 새로 만들어진 컨테이너에서 그 환경을 재설정**해줘야한다.

또한, 특정 사용자는 컨테이너 내에 현재 필요에 따라 수정한 것 그대로 다시 이미지화 시켜 

새로운 버전을 만들거나 다른 서버 혹은 로컬환경에서 그대로 구동해 보고 싶을 수도 있다.

Docker 엔진에서는 **Commit 기능**을 통해 이를 지원해준다.

[`티스토리 포스팅을 참고`](https://zunoxi.tistory.com/51)