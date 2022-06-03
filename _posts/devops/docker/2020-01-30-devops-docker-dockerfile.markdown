---
layout: post
title: Docker#3. 도커 파일(Dockerfile) / 이미지 생성
subtitle: 도커 파일(Dockerfile) / 이미지 생성
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---


해당 포스팅은 [https://zunoxi.tistory.com/26?category=950190](https://zunoxi.tistory.com/26?category=950190)으로 이전되었습니다.


<!--
이번 포스팅에서는 `Dockerfile`에 대해 **이해 및 제작**하고 도커의 이미지파일을 생성함으로서 서버에서 구동할 수 있게끔 일련의 준비물들을 마련하는 과정에 대해 포스팅한다.

<br>

---

**1\. Dockerfile이란?**

<br>

이제 **dockerfile**이라는 것을 만들어 볼텐데, dockerfile이라는 것은 도커 이미지를 만드는 과정에서 사전에 '`요거요거를 기반으로 이미지를 만들어라`' 라고 방법을 지정해 주고 해당 파일을 기반으로 docker 이미지 파일을 제작할 수 있게한다. 또한, dockerfile에서는 베이스가 되는 이미지를 만들고 각종 미들웨어의 이미지들을 설치하거나 이를 기반으로 어플리케이션의 구동하게끔 한다.

테스트는 현재 필자가 개발하고 있는 **React** 프로젝트를 이용해서 예를 들어 보려한다.

<br>

![그림1](/assets/img/devops/docker/dockerfile/1.png)
[React에 Dockerfile.dev는 원래 없습니다..!]

<br>

리액트 프로젝트를 작업중인 Git에서 내려받은 상태이다. 원래 react는 "node\_modules"라는 의존성 node 패키지 폴더가 있지만, 해당 폴더의 용량과 파일개수가 꽤나 크기때문에 .gitignore의 설정을 통해 git server에는 올리지 않았다. 또한, 도커로 이미지 생성 시 패키지 파일을 package.json에 있는 라이브러리들을 다 내려받고 새로 설치 하기 때문에 더욱이 올릴 필요가 없다.

<br>

```
# React image 파일 예시

From node:10

WORKDIR /src/app

COPY . .

RUN yarn install

CMD ["yarn","run","start"]
```

<br>

React image 빌드를 위한 Dockerfile내용인데, 일반적으로 사용하는 Dockerfile의 속성들에 대해 기술한다. 

<br>

\- **FROM** : 이 부분은 docker 이미지를 생성할 때 Docker Hub에 있는 특'정 이미지를 기반으로 이미지 생성하겠다.' 라고 선언 해주는 부분이다. **반드시 선언**해야하는 부분이며 여기서 선언한 이미지를 베이스 이미지라고 한다. 필자같은 경우는 리액트 프로젝트를 도커이미지로 만들기 위해 node를 베이스 이미지로 선언했다. 

\- **WORKDIR** : 이는 run, cmd 명령어가 실행될 디렉토리를 설정하는 부분이다. Dockerfile에서 정의한 명령어를 실행하기 위해서 작업용 디렉토리를 미리 선언해 준다고 이해할 수 있다.

\- **COPY** : 특정 파일을 도커 이미지에 추가한다. 필자는 WORKDIR로 지정한 위치에 현재 dockerfile이 위치한곳의 모든파일(dockerfile이 있는 폴더)를 모두 복사하기 위해 위와 같은 방식으로 표기했다.

\- **RUN** : 베이스로 선언했던 이미지 위에 환경을 구축하기 위한 명령을 실행하는 것이다. 필자는 'node 10.ver' 환경에 'yarn install'을 실행시킴으로써 **COPY**에서 복사했던 폴더 내의 package.json파일을 기반으로한 node.js의 라이브러리를 설치 할 것이다. 

<br>

>  RUN을 사용할 때는 다음과 같은 두가지의 방법이 있다.

1\. '`Shell`' 형식 : 리눅스 쉘에서 실행하는 것과 동일하다. 컨테이너 안에서 /bin/sh (기본쉘)를 사용한 것과 동일하게 작동한다.  별도로 기본 쉘을 바꿀 수 있는 'SHELL' 명령어도 제공한다.

2\. '`Exec`" 형식 : Shell 방식과 다르게 쉘을 거치지 않고 직접 실행한다. Exec 형식에서는 실행하고 싶은 명령을 JSON으로 지정한다. 또한 환경변수를 사용할 수 없다는 특징이 있다.

\- **CMD** : 최종적으로 생성된 이미지에서 명령을 실행 시킬 때 사용한다. Dockerfile내에서는 한개의 '**CMD**' 속성만을 사용할 수 있다. CMD 명령어는 Exec, Shell, Entrypoint 총 3가지 방법으로 사용할 수 있다. 위의 예시처럼 필자는 Exec 방식을 사용하여 JSON타입으로 React 서버를 실행시켰다.

\- **ENTRYPOINT** : CMD는 도커 컨테이너가 구동될 시 실행되는 속성으로, 컨테이너가 돌아갈때 별도의 설정으로 CMD 속성보다 더 우선적으로 실행 시킬 수 있는 특징이 있다. 반면 ENTRYPOINT같은 경우는 별도의 설정을 줘도 항시 먼저 선행되는 속성이므로 항시 필수로 컨테이너에서 돌아가야하는 설정은 ENTRYPOINT로 설정하는 것이 더 효율적일 수 도 있다.

<br>

참고로 보통 리액트는 build 이후 `webserver`와 함께 실행 시켜서 사용한다. 위 처럼 서버 자체로 기동시 노드엔진으로 기동하는것인데, 이는 개발서버로 테스트가 가능하지만 웹서버에 비하면 안정성이떨어지고 비효율적이므로 `Apache`나 `Nginx`를 사용하여 배포하는것이 좋다. 참고 [링크](https://codechacha.com/ko/deploy-react-with-nginx/)


<br>

+ 위 내용을 기반으로 "`Dokcerfile.dev`" 로 저장하면 이를 이용하여 도커이미지 파일을 생성할 수 있다. 기본적으로 Dockerfile을 생성하고 저장할때 파일명 자체를 확장자없이 'Dockerfile'이라는 이름으로 생성 할 경우, docker이미지 빌드 시 참조할 dockerfile을 적어주지 않아도 되는 편리함이 있다. 


<br>

---

**2\. 도커 이미지 파일 생성**

<br>

![그림2](/assets/img/devops/docker/dockerfile/2.png)

<br>

자, 이제 이미지 파일을 만들어 보려고 한다. 우선 Dockerfile이 있는 폴더에서 터미널창을 열고, 필자의 경우package.json을 참고해야 하고 경로 또한 확인 해야 하므로 react 프로젝트가 있는 위치에서 빌드를 진행한다.

```
docker build -f Dockerfile.dev -t zunoxi .
```

docker 이미지 파일을 만드는데에 있어 -f는 참조할 docker 파일명, -t는 이미지파일에 붙일 태그를 생성한다. 위에서 기술했던것 처럼 도커파일이름 자체가 Dockerfile이면 넘어가도 되는 부분이다.

(뒤에 꼭 "." 붙여줘야 한다!!! 이는 도커파일이 현재 실행되고 있는 디렉토리에 있다는 뜻이다.)

<br>

![그림3](/assets/img/devops/docker/dockerfile/3.png)

<br>

다음, docker image가 잘 만들어졌는지 터미널 창에서 확인해 보자.

```
docker images
```

<br>

![그림4](/assets/img/devops/docker/dockerfile/4.png)

<br>

이렇게 zunoxi라는 레포지토리가 생성된것을 알 수 있다. 이제 다음 4부에서 이를 push, pull 하며 Docker Hub을 이용해 서버에 바로 실행할 수 있는 환경을 만들어 보자.

-->
