---
layout: post
title: Web 9. 스프링부트(Spring-boot) jar 파일 만들기
subtitle: 스프링부트(Spring-boot) jar 파일 만들기
categories: programming
tags: programming web
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요
> `스프링부트(spring-boot)`프로젝트를 `Docker`로 구동해보기
  
- 목차
	- [`리눅스 메모리 사용량 확인`](#리눅스-메모리-사용량)
	- [`buffer? cache?`](#buffer-cache)
	- [`Top활용 프로세스별 메모리 확인`](#top활용-프로세스별-메모리-확인)
  
## Spring-boot to Docker?
---
앞선 포스팅에서 우리는 스프링부트 프로젝트를 jar로 만들었다. [https://zunoxi.tistory.com/68](https://zunoxi.tistory.com/68) 포스트에 이어서 해당 jar파일을 이용해 도커 이미지를 만들어보자. 

<br>

> _**본문에 앞서, CI/CD pipeline이 잘구축된 시스템에서는 jenkins상에서 maven 혹은 gradle로 빌드를한 이후에 Docker image를 만들고 이를 활용하는게 일반적인걸로 알고있다. 다만, 본 포스팅에서는 단순히 스프링프로젝트를 도커이미지로 변환시키는 방법에 대해서 설명하고자하기에 다른 빌드방법에 대한 내용은 생략하려한다.**_ 

<br>


### **1\. dockerfile 만들기**

<br>

먼저, docker는 윈도우 환경이나 docker가 설치된 리눅스 환경에서만 build가 가능하므로 `개발환경`부터 반드시 확인해야 한다.

`참고)` 원래 윈도우에서는 docker사용이 안됐고 `리눅스에서만 사용이 가능했다`.  
그러나 현재는 MS의 지원으로 윈도우 10 pro 이상은 하이퍼바이저 + docker desktop을 사용하고 그 이하는 docker toolbox를 사용함으로 윈도우에서도 docker를 사용 가능하다. (vm을 쓰는 것과 같은방식이다.)

<br>

[https://spring.io/guides/gs/spring-boot-docker/](https://spring.io/guides/gs/spring-boot-docker/)
스프링 부트를 도커 이미지로 만드는 방법은 위의 사이트에 잘 정리가 되어 있어 이번 포스트를 작성시 많은부분 참고했다. 해당 사이트를 기반으로 Dockerfile을 만들어보려한다.

<br>

**"Dockerfile"**

```
# openjdk8을 기반 이미지로 설정
FROM openjdk:8-jdk-alpine

# 호스트의 volume 설정
VOLUME /tmp

# build시 사용되는 변수 설정
ARG JAR_FILE=*.jar

# 컨테이너에 파일 복사
COPY ${JAR_FILE} app.jar

# 실행 명령 정의
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]

```

가장 윗 줄부터 `정리해서 설명`하자면

(1) FROM openjdk:8-jdk-alpine : `openjdk8`을 해당 컨테이너의 베이스 이미지로 설정한다.

(2) VOLUME /tmp : 컨테이너의 /tmp 디렉토리를 호스트의 특정 폴더와 공유한다.
volume 기능은 실제 운영을 위해서는 꽤나 중요하다고 생각하는데, 아래 포스트를 보고 참고하면 좋을 것 같다.

[https://zunoxi.tistory.com/50?category=871486](https://zunoxi.tistory.com/50?category=871486) (본 포스팅에서는 테스트만 진행할 예정으로 별도 볼륨 기능에 대해서는 서술하지 않는다.)

(3)  ARG JAR\_FILE=\*. jar : ARG는 컨테이너 내에서 사용할 수 있는 `변수`를 지정할 수 있다. 

필자는 스프링 프로젝트의 `target`만 서버에 올렸고, 해당 폴더에 Dockerfile을 생성했으므로 Dockerfile기준으로 폴더 내의 \*. jar를 선언하는 것은 스프링 부트의 빌드된 jar  파일을 의미한다. 

(4) COPY ${JAR\_FILE} app.jar : 위에 선언했던 JAR\_FILE 변수를 컨테이너의 app.jar로 복사한다.

(5) ENTRYPOINT \["java", "-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"\] : 실행할 명령을 정의.

컨테이너가 구동될 때 java -Djava.security.egd=file:/dev/./urandom -jar /app.jar 명령이 실행된다.

> 가운데 명령어(-Djava.security.egd=file:/dev/./urandom)는 JDBC Driver에서의 암호화 작업을 의미하는데 아래 포스트에 해당 내용을 참고하면 좋을것 같다.  
[https://velog.io/@skygl/Docker-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%EC%82%AC%EC%9D%B4%ED%8A%B8-%EA%B0%80%EC%9D%B4%EB%93%9C%EC%9D%98-Docker-%EC%97%B0%EB%8F%99%ED%95%98%EB%8A%94-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0](https://velog.io/@skygl/Docker-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B6%80%ED%8A%B8-%EC%82%AC%EC%9D%B4%ED%8A%B8-%EA%B0%80%EC%9D%B4%EB%93%9C%EC%9D%98-Docker-%EC%97%B0%EB%8F%99%ED%95%98%EB%8A%94-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)

<br>

---

### **2\. Docker image build**


(1) docker image build

공식문서나 여러 블로그 포스팅에서 `Maven기반` 혹은 `Gradle기반`에 따라 명령어를 나눠서 기술하고 있다. 필자는 Maven기반의 스프링 부트 프로젝트이며 프로젝트 폴더 안에서의 빌드가 아닌 앞서 말했던 것처럼, target 폴더만 서버에 올린 상태이므로 다음과 같은 명령어를 사용하여 빌드했다.

<br>



```
$ docker build -f Dockerfile -t zunoxi .
```

간략하게 옵션 명령어에 대해 설명하자면

- \-f : build에 참고할 파일명 (일반적으로 Dockerfile이라는 이름으로 도커 파일을 만들었다면 생략해도 무방하다)

- \-t : 이미지에 대한 태그

- " . " : 마지막에 . (온점)을 꼭 붙여줘야 한다. 해당 빌드 위치기반으로 이미지를 빌드하겠다는 뜻이 된다.

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/spring/1.png)

`공식문서`에서는 다음 명령어를 사용하라고 되어있으니 참고하면 좋을 것 같다. (프로젝트의 루트에서 실행)

```
$ docker build -t springio/gs-spring-boot-docker .
```
<br>

---
(2) docker image run > docker container 구동

```
$ docker run -p 9090:9090 --name spring [image id]
```

- \-p : 외부 포트:내부 포트 매핑 (외부에서 8080번으로 요청된 트래픽은 해당 컨테이너의 8080에 매핑시키겠다는 뜻)

- \--name : 생성할 도커 컨테이너의 식별자 이름을 지정

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/spring/2.png)

(이미지가 정상적으로 구동되는것을 확인 할 수 있다.)

---

(3) 컨테이너 정상 구동 여부 확인

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/docker/spring/3.png)


이렇게 `스프링부트 프로젝트`를 `docker image`로 만들었다. 말이 스프링부트이지 그냥 jar 파일을 docker 컨테이너안에서 실행시킬 수 있게끔 구현한 것이다. 다만 스프링 레거시가 아닌 부트(boot)이기 때문에 WAS가 별도로 필요 없는 정도? 해당 포스팅이 관련 문제로 고민하는 독자에게 참고가 되길 바라며😌

<br>

> 다음 포스팅에서는 깃랩과 젠킨스를 이용하여 쿠버네티스상에 해당 프로젝트를 자동으로 배포해볼 예정이다.