---
layout: post
title: Docker 11. Docker Window 설치 오류(error during connect...)
subtitle: Docker Window 설치 오류 트러블슈팅
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

## 개요
> Docker Desktop을 Window 환경에 설치관련 트러블 슈팅
  
- 목차
	- [`장애내용`](#-장애내용)
	- [`해결방법`](#해결방법)
  
## Docker for Window
---
사내 업무용 윈도우 PC에 Docker 설치하려고 하니 처음보는 에러메시지가 출력되었다. 해당 오류를 트러블슈팅했던 내용을 기록한다.



<br>

### 장애내용
---

필자가 사용하는 윈도우 PC의 버전은 `윈도우10 enterpris`e였고, 따라서 **Docker Desktop**을 설치했다. (Window7이나 Window10 home 버전은 Docker toolbox설치 필요)

설치 이후 재부팅없이 docker desktop 앱을 실행시켰더니 오류메시지가 출력되었다. 찾아봤더니 Docker 윈도우 설치이후에는 재부팅을 해줘야한다고 한다. 그래서 급하게 재부팅 후 CMD 프롬프트에 `docker ps` 명령어를 입력해보니 아래와 같은 에러가 출력된다. 

```
error during connect: Get http://%2F%2F.%2Fpipe%2Fdocker_engine/v1.35/info: open //./pipe/docker_engine: The system cannot find the file specified. In the default daemon configuration on Windows, the docker client must be run elevated to connect. This error may also indicate that the docker daemon is not running.

```

### 해결방법

[https://github.com/docker/for-win/issues/1825](https://github.com/docker/for-win/issues/1825) 에서 해답을 찾았다. 아마 구글에 관련 오류를 검색하면 해당 글이 가장 먼저 뜰것이다.

해당내용을 참고하여 윈도우 PowerShell로 이동하여 아래 명령어를 입력한다.

```
cd "C:\Program Files\Docker\Docker"
./DockerCli.exe -SwitchDaemon

```

이후 뭔가 정상적으로 되는듯하더니 필자는 `동일한 오류가 또 반복`되었었다.

결국 제어판에서 Docker Window를 아예 `삭제시키고 재설치`..그리고 재부팅없이 위 해결방법을 PowerShell에 입력했더니 정상적으로 Docker가 깔린것을 확인했다.

<br>

![그림1](/assets/img/devops/docker/window/1.png)



---

