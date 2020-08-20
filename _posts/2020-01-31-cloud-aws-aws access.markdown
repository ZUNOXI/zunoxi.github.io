---
layout: post
title: cloud#1.AWS 서버 접속 + 배치파일 만들기
subtitle: AWS 서버 접속 + 배치파일 만들기
categories: cloud
tags: cloud cloud
comments: true
published: true
header-img: img/devops/docker/logo.png
---

이번 포스팅은 도커 환경을 설치 하기 전, 먼저 **AWS서버 환경에 접속**하는 방법에 대해 알아보고자 한다.

![그림1](https://zunoxi.github.io/assets/img/cloud/1.jpg)

<br><br>

**아마존 웹 서비스(Amazon Web Services : AWS)** 는 아마존에서 런칭한 클라우드 서비스로 최근 ICT인프라 시스템의 운영방식이 온프레미스방식 뿐만아니라 클라우드 시스템 사용이 대두됨으로 핫핫핫하게 쓰이고 있는 시스템이다.

일반적으로 AWS 클라우드로 서버를 구축한다면 **Amazon Elastic Compute Cloud(AWS EC2)**를 사용할 것이다. 이번 포스팅에서는 배치파일을 미리 만들고 pem 파일을 이용함으로 간단하게 AWS에 바로 접속할 수 있게 해볼예정이다.

---
<br><br>

**1\. PEM 파일 만들기**

자 먼저 AWS로 부터 받은 인증키를 pem인증방식으로 저장한다. 인증키는 아래사진과 같은 형식으로 되어있을것이다. 저장 형식은 반드시 모든파일로 바꾸고 **확장자를 .pem**으로 저장해야한다.
![그림2](https://zunoxi.github.io/assets/img/cloud/2.png)
<br><br>

**2\. 배치(Batch file)파일 만들기**

같은 폴더 안에 이런식으로 배치파일을 만들어 준다.

![그림3](https://zunoxi.github.io/assets/img/cloud/3.png)
<br><br>

\- echo on 은 실제 cmd 창을 키고 입력하는 현재 위치를 노출시킬 것인지 아닐지를 표시한다. 밑에 입력한 내용은 실제 cmd에서 ssh통신으로 입력하는 커맨드를 미리입력해 놓은것이다.

예시 : ssh -i ./[test.pem](test.pem) ubuntu@00.00.00.00(aws ip)

(ubuntu : hostname, ip : aws ip)

![그림4](https://zunoxi.github.io/assets/img/cloud/4.png)
<br><br>

그럼 이제 폴더는 이렇게 구성 될 것이고, 배치파일을 사용함으로서  기존에 CMD 창을 열어 입력할 것을 미리 작성하여 바로 접속하는방식이며 배치파일 실행 시

_**C:\\Zunoxi\\Desktop\\aws\_test>ssh -i ./[test.pem](test.pem) ubuntu@00.000.00.000**_

를 CMD에 입력한 것과 같은 결과가 나오게 된다.
<br><br>

**3\. AWS 접속하기**

생성된 배치 파일을 실행해본다. 

![그림5](https://zunoxi.github.io/assets/img/cloud/5.png)
<br><br>
자, 이렇게 AWS 서버에 접속된 것을 확인할수 있다.   

![그림6](https://zunoxi.github.io/assets/img/cloud/6.png)
