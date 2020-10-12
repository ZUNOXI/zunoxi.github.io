---
layout: post
title: devops(3) Kubernetes에 Jenkins서버 설치
subtitle: jenkins를 활용한 파이프라인 구축
categories: devops
tags: devops kubernetes
comments: true
published: true
header-img: img/devops/jenkins/install/jenkins.jpg
---

## 개요
> `Git push` 부터 `쿠버네티스` 파드 배포 까지 파이프라인 구축 &#10104;단계
  
- 목차
	- [`퍼시스턴트 볼륨(PV) 생성`](#1-퍼시스턴트-볼륨pv-생성)
	- [`jenkins Helm install`](#2-jenkins-helm-install)
	- [`NodePort 설정`](#3-nodeport-설정)
	- [`jenkins install`](#4-jenkins-install)
  
## Jenkins on k8s
---
지난 포스팅에서 쿠버네티스에 설치형서버인 깃랩(Git-lab)을 설치했다. 이제 형상관리도구는 준비가 되었으니 CI/CD의 핵심이라고할 수 있는, 젠킨스를 쿠버네티스에 설치해보려고 한다. 이역시 상당히 삽질(?)을 했으나, 깃랩을 설치할 때 보다는 빠르게 마무리 되어 매우 행복하다 😌

<br>

[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fzunoxi.github.io%2Fdevops%2F2020%2F09%2F28%2Fdevops-k8s-jenkins%2F&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

아래그림은 Helm 설치를 위한 포스팅에서 직접 그렸던 그림인데, 현재 가장 먼저 쿠버네티스에 helm과 Gitlab을 설치하고 현재는 **Jenkins 설치 단계**중에 있다고 이해하면될 것 같다. 여기에 추가적으로 아래 그림에는 없는 소나큐브(Sonarqube) / 품질 테스트 도구도 도입해볼 예정이다.

<br>

![그림1](https://zunoxi.github.io/assets/img/devops/jenkins/install/structure.png)

<br>

먼저 깃랩과 마찬가지로 퍼시스턴트 볼륨을 미리 생성해주어서 로컬의 특정위치에 마운트를 시키고 사용하려 한다. 

---

### 1. 퍼시스턴트 볼륨(PV) 생성

<br>

아래의 명령어로 yaml 파일을 생성한다.

```
$ vi pvjenkins.yaml
```

![그림2](https://zunoxi.github.io/assets/img/devops/jenkins/install/1.png)
<br>

필자의 경우는 k8s-node1의 용량이 꽤 차있는 상태여서 node2에 퍼시스턴트 볼륨을 마운트시켰다.

```
kubectl apply -f pvjenkins.yaml
```

![그림3](https://zunoxi.github.io/assets/img/devops/jenkins/install/2.png)
<br>

---
### 2. jenkins Helm install

<br>
필자는 jenkins라는 이름으로 helm install을 할 예정이다. 

```
$ helm install --name jenkins stable/jenkins
```

![그림4](https://zunoxi.github.io/assets/img/devops/jenkins/install/3.png)
<br>

![그림5](https://zunoxi.github.io/assets/img/devops/jenkins/install/4.png)
<br>

대략 이런식으로 설치가 되었다는 말이 나오는데(작아서 잘 안보일수도..), 여기서 확인해야할 부분은 

![그림6](https://zunoxi.github.io/assets/img/devops/jenkins/install/5.png)
<br>

위의 부분이다. 젠킨스는 처음 설치하고 로그인하는 화면에서 `초기비밀번호`를 입력하게 되어있는데 해당 명령어를 입력하면 초기비밀번호가 나온다. 나중에 exec -it으로 들어가서 확인하는 방법도 있지만, 번거로우니깐 여기서 쳐보고 나온 비밀번호를 어딘가에 복사해 놓자.

---
### 3. NodePort 설정

<br>

최초에 jenkins를 쿠버네티스에 설치하면 ClusterIP 설정으로 인해 외부에서는 접근할 수 없게 되어있다. 이는 내부에서만 접근이 가능하며 서비스의 기본 type이 **ClusyerIP**이기 때문에 외부에서 젠킨스 UI에 접근하려면 **NodePort**라는 type변경을 해줘야한다.(실제 운영할 시스템이면 loadbalancer type설정을 해주는것이 가장 바람직하다.)

![그림7](https://zunoxi.github.io/assets/img/devops/jenkins/install/6.png)
<br>

쿠버네티스 대시보드에서 보면 `jenkins`와 `jenkins-agent`의 서비스 현황을 볼 수 있는데, 우리가 변경해줘야할 네트워크 타입은 '**jenkins**' 서비스이다. 해당 서비스의 yaml 파일을 편집하다보면 아래처럼 type이 ClusterIP임을 확인할 수 있다.

![그림8](https://zunoxi.github.io/assets/img/devops/jenkins/install/7.png)
<br>

![그림9](https://zunoxi.github.io/assets/img/devops/jenkins/install/8.png)
<br>

해당부분은 위와 같이 수정해준다. type은 **NodePort**로 수정한다. 여기서 '**externalTrafficPolicy**" 설정을 고려해볼 수 있는데 Local로 설정하게되면 오직 로컬 엔드포인트로만 프록시를 요청하게된다. 다른 노드로 트래픽을 전달하지 않는다. 

즉, 보통 NodePort는 모든 노드에 포트를 만들기 때문에 서비스는 어떤 노드에게 온 트래픽이든 상관없이 본인에게 달려있는 파드들에게 트래픽을 주는데 해당 옵션이 이를 방지하고 특정 ip의 포트에게만 주는 기능을 제공한다. 필자같은 경우에는 적용하지 않았다.

![그림10](https://zunoxi.github.io/assets/img/devops/jenkins/install/9.png)
<br>

자, 다시 서비스 대시보드 창을 살펴보면 **32641번 포트**가 생긴것을 알 수 있다. 기존 외부에서 접근 할 수 없던 방식에서 아래 그림처럼 이제 32641번 포트로 접근 할 수 있게 되었다. 이제 해당서버의 32641번 포트로 접근해보자.

---
### 4. jenkins install

본격적으로 `젠킨스를 설치`하는 화면이다. 서버 ip:지정포트로 접근하면 해당 화면을 볼 수 있는데, 여기서 부터는 일반적인 젠킨스 설치와 같다. 즉, 쿠버네티스에 이미 **젠킨스 서비스는 배포중**이라는것이다. 이제 확실한 테스트를 위해서 마저 설치해보려한다.

![그림11](https://zunoxi.github.io/assets/img/devops/jenkins/install/10.png)
<br>

처음 젠킨스를 설치할때 비밀번호를 입력해야한다. 이부분은 아까 별도로 기록해놔야 한다는 '그 비밀번호'를 입력해주면 된다. 기억이 안나면 젠킨스가 설치되어있는 폴더(PV 마운트 해놓은 경우)나 컨테이너 안에 들어가서 확인해봐야한다.

로그인을 하면 아래와 같은 창이뜬다.

![그림12](https://zunoxi.github.io/assets/img/devops/jenkins/install/11.png)
<br>

여기서 부터는 Jenkins 관리 > plugin 설치로 들어가 본인이 필요한 플러그인을 설치해주면 되겠다. 

![그림13](https://zunoxi.github.io/assets/img/devops/jenkins/install/12.png)
<br>

필자는 깃랩과의 연동을위해 관련 플러그인을 설치 중인데, 각자가 필요한 플러그인을 설치하여 활용하면 될 것이다.

> 다음포스팅에서는 소나큐브를 설치하여 코드의 테스트 품질을 점검해보려한다 😌



