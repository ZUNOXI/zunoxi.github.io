---
layout: post
title: Docker#8 Commit 활용 컨테이너 이미지화
subtitle: Docker Commit 활용해서 컨테이너를 이미지화시키기
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요
> `Docker commit` 을 활용하여 `컨테이너`를 이미지화 시키기 
  
- 목차
	- [`톰캣실행`](#1-톰캣실행)
	- [`톰캣컨테이너내에 파일 복사`](#2-톰캣컨테이너내에-파일-복사)
	- [`톰캣컨테이너 이미지화`](#3-톰캣컨테이너-이미지화)
	- [`해당 이미지로 새로운 컨테이너 생성`](#4-해당-이미지로-새로운-컨테이너-생성)
	- [`새 톰캣컨테이너 확인`](#5-새-톰캣컨테이너에-파일이-그대로-복사-되어-있는지-확인)
  
## Docker commit
---
Docker 사용 시 가끔 컨테이너 내부에서 `프로그램의 환경을 수정`하는 경우가 생기는데, _**도커이미지는 읽기 전용**_ 이기 때문에 `컨테이너`에 수정된 설정데이터가 쌓이게 된다.이는 즉, 수정된 해당 컨테이너는 컨테이너 삭제시 변경했던 데이터가 모두 초기화 되는것을 의미한다.


<br>

예를 들어 지난 포스팅에서 다뤘던 tomcat이나 아파치 웹서버를 도커로 운영 할 시, 가상호스트 설정 혹은 웹프로그래밍 파일을 컨테이너 내에 적재한 상태에서 해당 컨테이너가 삭제될 때 **다시 이미지를 실행시키고 새로 만들어진 컨테이너에서 그 환경을 재설정**해줘야한다.

또한, 특정 사용자는 컨테이너 내에 현재 필요에 따라 수정한 것 그대로 다시 이미지화 시켜 새로운 버전을 만들거나 다른 서버 혹은 로컬환경에서 그대로 구동해 보고 싶을 수도 있다.`Docker 엔진`에서는 **Commit 기능**을 통해 이를 지원해준다.

<br>




이번 포스팅에서는 이 기능을 이용해서 톰캣이미지를 실행시키고 해당 컨테이너에서 수정한 내용을 그대로 이미지화 시켜보는것을 테스트 하려고 한다.

<br>

---

### **1\. 톰캣실행**

먼저 미리 만들어둔 tomcat\_test 라는 톰캣 컨테이너를 실행시킨다.

(다음 포스팅 참고 : [https://zunoxi.tistory.com/50](https://zunoxi.tistory.com/50))

컨테이너 실행 상태 확인


![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/1.png)

<br>

---

### **2\. 톰캣컨테이너내에 파일 복사**

현재 톰캣컨테이너로 접속했을 때 아래와 같다.

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/2.png)

톰캣에 태워서 보여 줄 간단한 웹페이지 파일을 복사

(복사는 다음 포스팅 참고 : [https://zunoxi.tistory.com/48?category=871486](https://zunoxi.tistory.com/48?category=871486))

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/3.png)

이런식으로 복사를 해준다.

이후, 다시 톰캣 컨테이너로 접속을 하면 아래와 같이 바뀐것을 알 수 있다.

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/4.png)

웹페이지 파일들이 정상 적용되었으니 해당 컨테이너를 이미지화 시켜서, 새롭게 컨테이너를 만들었을때도 위와 같이 적용되게 해볼 것이다.

---

### **3\. 톰캣컨테이너 이미지화**

```
docker commit tomcat_test tomcat:tomcat0529
```

> docker commit \[컨테이너 이름\] \[이미지명 혹은 id\]:\[태그버전 지정\]

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/5.png)

'tomcat0529' 태그가 적용된 톰캣 이미지가 생긴것을 알 수 있다.백업을 위해 + 버전관리를 위해 docker hub에 이를 push 하려 한다.

<br>

![그림6](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/6.png)
(도커 태깅이 먼저!)

<br>

![그림7](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/7.png)
(사전에 비슷한 파일을 올려놔서 금방 올라갔다)

<br>

![그림8](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/8.png)

Docker hub에도 해당 이미지가 전송된것을 확인 할 수 있다.

---

### **4\. 해당 이미지로 새로운 컨테이너 생성**

![그림9](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/9.png)

<br>

---

### **5\. 새 톰캣컨테이너에 파일이 그대로 복사 되어 있는지 확인**

![그림10](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/10.png)

새롭게 만들어진 컨테이너 내부에도 이전에 이미지화 시켰던 컨테이너의 웹페이지가 그대로 들어있다.

해당 컨테이너 톰캣을 브라우저에서 접속했을때,

![그림11](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/commit/11.png)

위와 같이 동일하게 나오는 것을 확인 할 수 있다!!