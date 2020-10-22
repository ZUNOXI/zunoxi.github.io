---
layout: post
title: Docker 5. CentOS7 Docker 데이터 경로 변경하기
subtitle: CentOS7 Docker 데이터 경로 변경하기
categories: devops
tags: devops docker
comments: true
published: true
header-img: img/devops/docker/logo.png
---

도커를 사용하다보면 서버의 root 폴더에 docker image와 컨테이너가 계속 쌓이게된다. 이런 환경에서 서버 운영체제를 다시깔게 되면 이미지 파일이 모두 날아갈 수 있고, 더 나아가 root 폴더에 용량이 꽉차면 아예 **서버가 멈춰버리는** 현상이 발생할 수 도 있다. 

쿠버네티스 실습을 위해 도커환경에서 이런저런 테스트를 하고 있는데 20기가 밖에 안되는 root폴더에 docker 이미지가 10개 이상 넘어가니 root가 꽉찬다.. 찾아보니 도커를 이용해서 실 운영을 하는 사람은 별도 NAS서버에 네트워크 마운트를 하거나, 서버내에 여유 디스크를 별도로 마운트해서 사용해야 한다고 한다. 맞는말이다.

경로를 바꿔보려고 책도 뒤져보고 구글링도 했지만 대부분 ubuntu나 centos 6버전 이하에 해당하는 솔루션이라 이 역시 삽질을 좀 했다.

(~나는 centos7을 쓰는걸...!!~)

그렇게 거의 1주일간의 삽질끝에 Centos7에서 docker의 데이터 경로를 변경했다.

필자처럼 CentOS7 에서 서버 운영하시는 분들은 내용 참고 하시길 바라며 :) 

---

**1\. docker 디렉토리 생성 혹은 복제**

기본적으로 도커의 이미지, 컨테이너등 데이터 파일들의 경로는 /var/lib/docker 디렉토리에 있다. )특히 overlay와 container 디렉토리에 있는 용량이 사악하다...!)

**변경할 경로에 docker라는 디렉토리를 미리 만들어두고 기존에 docker를 운영했다면 (필요에 따라)** **/var/lib/docker 디렉토리의 내용을 복제해 놓는걸 권장한다.** **(기존에 있던 컨테이너와 도커 이미지가 함께 복제되서 계속 사용할 수 있다.)**

---

**2\. daemon.json 수정**

자, 우리는 **daemon.json** 파일을 수정하며 도커 데이터 파일의 경로를 바꿔주도록 하겠다. '/etc/docker/daemon.json'에서 수정하면 되겠다. 

```
$ /etc/docker/daemon.json
```

```
{
    "graph": "도커 데이터 경로로 지정할 위치"  # 필자는 "graph": "/u01/docker" 로 사용했습니다.
}
```

필자같은 경우는 약 200GB 정도의 추가 디스크를 마운트하고 해당 위치에 저장했다.

(마운트과정에서 문제가 있었던건지, 경로 변경했던것이 잘 안먹어서 고생좀 했다ㅠㅠ 데이터 경로 변경할 곳이 잘 마운트가 되었는지 확인 잘하시고 설정하길..)

**\* 당연히 /u01/docker 라는 디렉토리는 사전에 만들어 놓아야 한다.**

그리고 도커를 재시작!

**3\. Docker 재시작 및 적용 확인**

아래 명령어를 차례로 입력해 준다.

```
sudo service docker stop

sudo systemctl daemon-reload

sudo service docker start
```

마지막 명령어에서 정상적으로 도커가 실행된 후, 기존에 도커를 썼던 사람은

```
docker images
```

라는 명령어를 입력하고 원래있던 이미지들이 잘 나온다면 정상적으로 데이터 경로가 바뀐것이다.

간단한 테스트를 위해 docker hub에서 이미지를 pulling 한 후에

```
df -h
```

명령어를 사용하여 지정한 디렉토리 위치의 용량이 늘어나는지 확인되면 완료이다 :)
