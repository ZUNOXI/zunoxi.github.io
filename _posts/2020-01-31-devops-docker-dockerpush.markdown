---
layout: post
title: Docker#4. 도커(Docker) Hub Push / Pull / Run
subtitle: 도커(Docker) Hub Push / Pull / Run
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

이번 포스팅에서는 이전 포스팅에서 만든 도커 이미지를 **Docker Hub** 상에 docker push와 docker pull을 이용하여 **로컬환경에서 개발한 어플리케이션**을 도커 이미지로 로딩함으로써 **서버에서도 구동**이 가능하게끔 해볼예정이다.

<br>

---

> **1\. Docker push 하기**

docker push 이전에 docker hub에 대해 설명해야 할 것 같다. **docker hub**는 gitlab이나 github처럼 코드를 관리하고 공유하는 시스템과 유사하며 **docker이미지를 공유 및 버전 관리**를 하게 해주는 `형상관리 툴 혹은 웹사이트`라고 이해 할 수 있다.

먼저, 필자 로컬에 만들어진 docker 이미지 파일들을 확인한다.

```
> docker images
```

<br>

![그림1](/assets/img/devops/docker/dockerpush/1.png)

<br>

"zunoxi:latest" 라는 이미지 파일을 docker hub에 push 해보려 한다.

<br>

![그림2](/assets/img/devops/docker/dockerpush/2.png)

<br>


Docker push 를 하려면 `Docker Hub의 계정`이 있어야함은 물론이고, 본인의 **Repository가 있어야 push 할 수 있다.** Github을 생각해본다면 올리고 싶은 프로젝트에 대해 git repository 생성이 안되어있는데 올릴수는 없는것과 같다. 필자는 javer 라는 ropository를 만들었고 이곳에 로컬에서 만든 image파일을 push할 예정이다.

먼저 docker hub에 업로드 할 수 있는 권한을 얻기 위해 로컬 터미널 창에 다음과 같은 명령어를 입력한다.

```
> docker login
```

이후 docker hub의 userID, password를 입력한 후, Login suceed가 확인된다면 docker image에 태그를 달아 보자.

\* **Docker image 파일 태그 걸기**

```
> docker tag zunoxi cross9308/javer:zunoxi
```

docker tag \[이미지 명\] \[docker hub id\]/\[repository 명\]:\[태그명\] 이렇게 입력한다. 그리고 터미널창에 $ docker images를 입력하면 cross9308/javer repository에 zunoxi 태그가 걸린 이미지가 확인된다.

**\* Docker image 파일 푸시 하기**

```
> docker push cross9308/javer:zunoxi
```

`docker push \[docker hub id\]/\[repository 명\]:\[태그명\] `

이부분에서 거의 반나절 넘게 해맸다... 자꾸 denied가 떠서...😢 stack overflow를 통해 겨우 해결했다. 정말 아무것도 아니였는데 이런 사소한 부분을 캐치해내지 못하는것이 실력있는 엔지니어와 그렇지 않은 사람을 구분하는 듯해서 마음이 아프다(?)

본인은 이미지 파일을 푸시하고싶은데 왜 안될까 싶은 사람은 아래 주소를 참고.

[https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker](https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker)


<br>

![그림3](/assets/img/devops/docker/dockerpush/3.png)

<br>

이렇게 Docker Hub repository 내부에 zunoxi가 정상적으로 업로드 된 것을 알 수 있다. 이제 docker pull을 이용해서 AWS 서버상에서 이미지를 구동해 보려한다.

<br>

> **2\. Docker Pull 하기**

<br>

AWS 서버에 접속하여 커맨드 창에 다음과 같은 명령어를 입력하여 pull 해보려한다.

**(Docker login 역시 마찬가지로 사전에 해줘야 한다!)**

```
> docker pull cross9308/javer:zunoxi
```

docker pull\[docker hub id\]/\[repository 명\]:\[태그명\] 

<br>

![그림4](/assets/img/devops/docker/dockerpush/4.png)

<br>

이렇게 정상적으로 이미지를 당겨왔다.😎

<br>

![그림5](/assets/img/devops/docker/dockerpush/5.png)

<br>

이제 해당 이미지를 실행 시켜보려한다.

<br>

> **3\. Docker image run**

<br>

실행은 다음과 같은 명령어로 간단하게 실행 할 수 있다.

```
$ docker run -p 3000:3000 944f15368886
```

`docker run -p \[호스트의 포트 번호\]:\[컨테이너의 포트 번호\] \[docker imageID\]` 

docker container run의 옵션 중 네트워크 설정에 해당하는 부분이 꽤 많지만, 해당 포스팅에서는 **\-p or --publush** 에 대해서만 다룬다. 위의  예시 처럼 `-p 옵션은 호스트와 컨테이너의 포트를 매핑`시키는것이며, 풀어서 설명하자면 호스트 서버의 3000번 포트로 트래픽요청이 들어오면 이를 컨테이너의 3000번포트로 연결시키는것을 의미한다. 

다른 예로 8080으로 돌아가는 어플리케이션을 컨테이너로 구동시키고 4500:8080 으로 매핑을 하면 호스트의 4500번 포트로 컨테이너의 어플리케이션에 접근 가능하다. (물론 방화벽은 열어줘야한다.)

<br>

추가로 docker run 시 **\-d 옵션**을 사용하면 백그라운드 모드로 실행된다.

<br>

![그림6](/assets/img/devops/docker/dockerpush/6.png)

<br>

![그림7](/assets/img/devops/docker/dockerpush/7.png)

<br>

이렇게 잘 돌아가는 모습을 볼 수 있다. 여기까지 총 4부에 걸쳐 도커의 개념부터 도커를 활용하여 서버에서 프로젝트를 구동하는 과정까지 정리 해봤다. 

<br>

---

※ 앞선 포스팅에서도 언급했지만 react로 개발한 프론트를 운영계에 배포할때는 이런식으로 돌리는것 보다 빌드를 마치고 웹서버와 함께돌리는것이 맞다.(nodejs는 웹서버가 아니니깐) 

따라서 빌드된 react 프로젝트를 nginx나 apache같은 웹서버를 이용하여 도커 이미지를 만드는것이 맞겠지만, 이번 포스팅에서는 최대한 간단하게 진행해보려 포스팅을 작성했었으니 그부분 참고하면 좋을 것 같다.

ps. 혹시라도 과정 중 궁금증이 생기거나 수정해야할 부분이 있다면 언제든지 댓글달아 주세요 🙌🙌🙌
