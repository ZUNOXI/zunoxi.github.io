---
layout: post
title: Docker 3. 도커 파일(Dockerfile) / 이미지 생성
subtitle: jenkins를 활용한 파이프라인 구축
categories: devops
tags: devops jenkins
comments: true
published: true
header-img: img/devops/jenkins/install/jenkins.jpg
---

이번 포스팅에서는 Dockerfile을 제작하고

도커의 이미지파일을 생성함으로서 서버에서 구동할 수 있게끔 준비물들을 마련하겠습니다 😎

[##_Image|kage@MGrrV/btqBCn0gi1d/ogp5v2tI73qoVobTCoY9x1/img.png|alignCenter|data-filename="dockericon.png" data-origin-width="792" data-origin-height="269" data-ke-mobilestyle="widthContent"|||_##]

먼저 간단하게 Docker 환경을 체크해 볼 수 있는 명령어들을 알아보겠습니다.

\* 도커 버전 확인

```
docker version
```

\* 도커 실행 환경 확인

```
docker system  info
```

\* 도커 디스크 이용 현황 확인

```
docker system df
```

\* 실행중인 도커 이미지 파일 확인

```
docker ps 
```

\* 실행중인 도커 이미지 파일 중지 

```
docker stop [도커 id]
```

**1\. Dockerfile 만들기**

자, 이제 **dockerfile**이라는 것을 만들어 볼텐데요!🤔

dockerfile이라는 것은 도커 이미지를 만드는 과정에서 사전에 '요거요거를 기반으로 이미지를 만들어라'

라고 방법(?)을 지정해 주고 해당 파일을 기반으로 docker 이미지 파일을 제작할 수 있게합니다. 

테스트는 현재 제가 개발하고 있는 **React** 프로젝트를 이용해서 예를 들어 보겠습니다.

[##_Image|kage@bp5qhn/btqBDb59c8j/Gq6uPfWkS0vCq1wKxxITg0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|Dockerfile.dev는 원래 없습니다...!!||_##]

리액트 프로젝트 폴더내의 모습입니다. 원래 "node\_modules" 폴더가 있어야 하지만,

도커로 이미지 생성 시 패키지 파일을 package.json에 있는 라이브러리들을 다 내려받고 새로 설치 하기 때문에

제외하고 깃에 업로드 중이라... 없습니다 ^ㅠㅠ^  😁

```
From node:10

WORKDIR /src/app

COPY package*.json ./

RUN yarn install

COPY . .

CMD ["yarn","run","start"]
```

Dockerfile내용입니다.

\- **FROM** : 이 부분은 docker 이미지를 생성할 때 Docker Hub에 있는 해당 이미지를 기반으로 이미지 생성하겠다!

라고 선언 해주는 부분입니다. 저는 리액트 프로젝트를 도커이미지로 만들기 위해 node:10이라고 작성했습니다.

\- **WORKDIR** : 이는 run, cmd 명령어가 실행될 디렉토리를 설저하는 부분입니다.

\- **COPY** : 파일을 이미지에 추가합니다. 저는 package.json을 추가함으로 파일에 명시되어있는

라이브러리가 다 추가될 수 있도록 설정했습니다.

\- **RUN** : 우리가 구상할 이미지에 다른패키지를 설치하는 것입니다.

저는 node:10 환경에 yarn install을 함으로써 package.json의 라이브러리를 install 하게 될것입니다. 

\- **CMD** : docker run을 실행시킬 시 도커에 사용할 default 명령어를 주는 부분입니다.

[(https://khj93.tistory.com/entry/Docker-Docker-File-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0-%EB%AA%85%EB%A0%B9%EC%96%B4](https://khj93.tistory.com/entry/Docker-Docker-File-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0-%EB%AA%85%EB%A0%B9%EC%96%B4)를 참고하시면 더 도움이 되실것 같아요! )

자 메모장에 제가 작성한것 처럼 쓰고 "Dokcerfile.dev" 로 저장하면 이를 이용하여

도커이미지 파일을 생성할 수 있습니다.

**2\. 도커 이미지 파일 생성**

[##_Image|kage@bp5qhn/btqBDb59c8j/Gq6uPfWkS0vCq1wKxxITg0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

자, 이제 이미지 파일을 만들어 볼까요???😊

우선 Dockerfile이 있는 폴더에서 터미널창을 엽니다. 저는 package.json을 참고해야 하고

경로 또한 확인 해야 하므로 react 프로젝트가 있는 위치에서 빌드를 진행하겠습니다.

```
docker build -f Dockerfile.dev -t zunoxi .
```

docker 이미지 파일을 만드는데에 있어 -f는 참조할 docker 파일명, -t는 이미지파일에 붙일 태그를 생성합니다.

(뒤에 꼭 "." 붙여주셔야 합니다!!! 이는 도커파일이 현재 실행되고 있는 디렉토리에 있다는 뜻입니다.)

[##_Image|kage@cIwjgQ/btqBCUqIBs2/L6NYssJSFWQzwf8jyyKR2K/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|사전에 제가 만들어 놓은이미지와 동일 했던것이라 매우 빨리 됐습니다..!||_##]

다음, docker image가 잘 만들어졌는지 터미널 창에서 확인해 봅시다.

```
docker images
```

[##_Image|kage@x1j8H/btqBDYzqpSV/k7AY44E0rZQLL9SIJTVIpK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

이렇게 zunoxi라는 레포지토리가 생성된것을 알 수 있습니다.

이제 다음 5부에서 이를 push, pull 하며 Docker Hub을 이용해 서버에 바로 실행할 수 있는 

환경을 만들어 보기로 합시다! 😆

[##_Image|kage@c3Dv9X/btqBFvQBHkt/RI2jCDBM6k81V6CmGkpzn1/img.png|alignCenter|data-filename="다운로드 (2).png" data-origin-width="1280" data-origin-height="1141" data-ke-mobilestyle="widthContent"|||_##]