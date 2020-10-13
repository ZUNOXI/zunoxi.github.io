---
layout: post
title: CentOS7에 쿠버네티스 설치하기
subtitle: CentOS7에 kubernetes 설치
categories: devops
tags: devops kubernetes
comments: true
published: true
header-img: img/devops/k8s/install/logo.png
---

## 개요
> `Centos7`에 `Kubernetes` 클러스터 설치하기
  
- 목차
	- [`DevOps구조`](#devops구조)
	- [`Helm?`](#helm)
	- [`Why Helm`](#why-helm-)
	- [`How to install`](#how-to-install-)
  
## Kubernetes 설치
---
리눅스에 대한 기초가 부족해서 거의 1주일이 넘게 쿠버네티스 설치에 고구마 1000개 정도 먹은것 같았는데, 마침내... 쿠버네티스 대시보드에 진입하게되어 세상기쁘다.😌 필자처럼 고생하는 분들이 없길 바라며 설치했던 과정을 포스팅해보려한다.

<br><br>



[##_Image|kage@mMx0O/btqDL4DiRjE/ABnzClBL6aJCcGBBEnbQFk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|똿..!||_##]

이번 포스팅은 현재 쿠버네티스 강의로 듣고있는 **김태민님의 인프런강의**를 참고하며 포스팅을 작성했다. 쿠버네티스관련 국내 강의가 별로 없는데 쿠린이 입장에서는 환경구축에 큰 도움이 되었다. 쿠버네티스를 **온프레미스** 환경에 구축하려고 하는사람들에게 추천하고 싶은 강의이다. 단, 자세하고 친절한 설명으로 구성된 강의이나 필자의 OS 버전이 다르고 pc 환경이 다르니 별도로 손봐야할것들이 좀 있었다.

참고했던 김태민님의 블로그와 CUBRID 블로그

[https://kubetm.github.io/practice/appendix/installation\_case1/](https://kubetm.github.io/practice/appendix/installation_case1/)

[

KubeTM Blog with Kubernetes

Kubernetes Blog for Inflearn Pratice

kubetm.github.io



](https://kubetm.github.io/practice/appendix/installation_case1/)

[https://www.cubrid.com/blog/3820603](https://www.cubrid.com/blog/3820603)

[

CUBRID | 블로그 - Docker, Kubernetes 환경에서 CUBRID 컨테이너 서비스 해보기

Docker, Kubernetes 환경에서 CUBRID 컨테이너 서비스 해보기 최근에 여러 기업에서 Docker, Kubernetes를 사용하여 컨테이너로 서비스 하는 사레들을 많이 보았습니다. CUBRID도 컨테이너로 만들어서 Docker, Ku

www.cubrid.com



](https://www.cubrid.com/blog/3820603)

---

**해당 포스팅의 실습에서는 **개인용** 노트북(or 데스크탑) + VM 조합이 아닌 vSphere를 이용한 서버 클러스터에 VM을 몇개 만들어서 진행해 봤다.**

_작업환경 : CentOS Linux release 7.7.1908 (Core)_

---

**1\. 서버 가상화**

VM으로 실습 할 마스터 1개(마스터 노드)와 미니언 노드(워커 노드) 2개를 만든다. 사실, 한개의 서버에 설정을 다하고 그대로 vm 여러개로 복사하는 방법이 더 효율적이기 때문에, 먼저 설정을 다하고 vm째로 복사하는것을 추천한다. 

virt-Manager와 kvm을 이용하면 리눅스 pc에서 가상머신을 만들수 있다. 단, 여기서 중요한것은 본인의 PC나 노트북이 가상화 기능을 지원하는지 확인이 필요하다. 가상화 기능을 지원하지 않는다면 여러 VM을 띄울수 없기때문에 **minikube**를 이용해야 한다.

[https://kubernetes.io/ko/docs/tasks/tools/install-minikube/](https://kubernetes.io/ko/docs/tasks/tools/install-minikube/)

[

Minikube 설치

이 페이지는 단일 노드 쿠버네티스 클러스터를 노트북의 가상 머신에서 구동하는 도구인 Minikube의 설치 방법을 설명한다. 시작하기 전에 리눅스맥OS윈도우리눅스에서 가상화 지원 여부를 확인��

kubernetes.io



](https://kubernetes.io/ko/docs/tasks/tools/install-minikube/)

필자는 앞서 말한것 처럼 vSphere를 사용해서 VM을 3개 생성하고 이를 클러스터링했다. 이와 별개로 개인 리눅스 PC에서 가상화 시키는 부분은 구글에 많은 방법이 있으니 각 pc의 사정대로 맞춰서 설치하면 될것.

---

**2\. VM Centos를 설치**

일단 한개의 서버에 vm을 통해 총 3개의 가상서버를 만드는 과정과 방법이 완료되면, 이제 VM 내부 사전 설정을 할 차례이다. Centos7을 운영체제로 선택하여 설치해 주자. 개인 PC에서 vm을 이용해서 실습하는 경우에는 다운 받은 centos7을 이용하여 설치할 수 있다.

centos7은 아래 링크에서 다운로드 받을 수 있다.

[http://isoredirect.centos.org/centos/7/isos/x86\_64/](http://isoredirect.centos.org/centos/7/isos/x86_64/)

[

CentOS Mirrors List

isoredirect.centos.org



](http://isoredirect.centos.org/centos/7/isos/x86_64/)

---

**3\. VM 세팅**

(1) Selinux 설정 

\* selinux는 미국 국방부 스타일의 강제 접근 제어(MAC)를 포함한 접근 제어 보안 정책을 지원하는 리눅스 커널 보안 모듈이다.(보안 강화 리눅스(Security-Enhanced Linux)의 약자)

쿠버네티스에서는 Pod Network에 필요한 호스트 파일 시스템에 액세스가 가능하게 하는 설정이라고 한다.

```
vi /etc/sysconfig/selinux
```

이곳으로 들어가서 SELINUX=disabled 을 SELINUX=permissive 로 변경후 저장한다. (**reboot 해야 적용된다!**)

```
sestatus   # 이 명령어로 상태를 확인하자
```

(2) 방화벽 해제

firewalld 비활성화 (CentOS 7의 방화벽 관리 데몬은 firewalld 이다.)

```
systemctl stop firewalld && systemctl disable firewalld
```

NetworkManager 비활성화

```
systemctl stop NetworkManager && systemctl disable NetworkManager
```

(3) SWAP 비활성화

```
swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab
```

(4) Iptables 커널 옵션 활성화

Centos7 사용 시 iptables가 무시되서 트래픽이 잘못 라우팅 되는 문제가 발생한다고 한다. 

```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

(5) 쿠버네티스 YUM Repository 설정

(yum은 Centos7의 패키지 관리 시스템이며 파이썬으로 설계되어있다.)

```
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
```

(6) Centos Package Update

```
yum update
```

(7) Hosts 등록

호스트 미설정 시 kubeadm init에서 Host이름으로 IP를 찾을 수 없다는 에러가 뜬다.

\* Kubeadm은 쿠버네티스 클러스터의 가장 빠른 설치를 도와주는 쿠버네티스 기본구성요소이다.

(참고 : [https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/))

```
cat << EOF >> /etc/hosts
[master의 IP] k8s-master
[node1의 IP] k8s-node1
[node2의 IP] k8s-node2
EOF
```

---

**4\. Docker와 Kubernetes 설치**

(1) 도커 설치 전 사전 세팅

```
yum install -y yum-utils device-mapper-persistent-data lvm2 
```

(2) 도커 저장소 설정

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

(3) 도커 패키지 설치

```
yum update && yum install docker-ce-18.06.2.ce
```

(4) 쿠버네티스 설치

```
yum install -y --disableexcludes=kubernetes kubeadm-1.15.5-0.x86_64 kubectl-1.15.5-0.x86_64 kubelet-1.15.5-0.x86_64
```

**※ 여기서 중요한것은 1.15 버전으로 설치해야한다는 것이다.**

처음에는 현재최신버전보다 낮다고 뜨길래 1.18 버전으로 변경해서 설치했다가 대시보드 1.v 대가 맞지 않아서 고생을 좀 많이했다. 현재 확인결과, 쿠버네티스 1.16 이상버전은 대시보드 2.0을 사용해야 한다.

(김태민님의 Q&A에서 알게된것..!)

대시보드 2.0에서는 보안이 강화되어 토큰이나 kuberconfig 같은 인증을해야하는데, 그러기 위해서는 kubernetes-dashboard namespace를 수정해야한다. 근데 왜 내가 하면 또 안되는지 모르겠다...방법을 고민하다가 아예 그냥 쿠버 1.15 버전 + 대시보드 1.0 버전으로 다운그레이드 해봤다. 

(쿠버 1.6이상의 버전에서 대시보드를 사용하시는 분은 다음 링크를 참조하면 좋을것 같다.)

[https://github.com/kubernetes/dashboard](https://github.com/kubernetes/dashboard)

[

kubernetes/dashboard

General-purpose web UI for Kubernetes clusters. Contribute to kubernetes/dashboard development by creating an account on GitHub.

github.com



](https://github.com/kubernetes/dashboard)

---

**5\. VM clone**

사실 나는 이미 가상서버 3개를 만들어 놓고 테스트를 했었기에 vm clone은 하지 않고 그냥 3번씩 반복해서 node(워커노드) 2개에 동일한 환경을 만들어줬다. 좀더 효율적이게 하려면 위 참고 링크로 걸어놓은 블로그를 참고하여 가상머신을 복제하는것이 좋을듯...!

---

**6.  Master node 설정**

(1) 도커 실행

```
systemctl daemon-reload
```

```
systemctl enable --now docker
```

(2) 쿠버네티스 실행

```
systemctl enable --now kubelet
```

(3) 쿠버네티스 초기화 실행

kubeadm init을 사용하여 쿠버네티스 클러스터를 빠르게 구축해 주자.

```
kubeadm init --pod-network-cidr=172.16.0.0/16
```

" 위 명령어를 실행하면 "kubeadm join xxx.xxx.xxx.xxx....." 라는 메세지출력된다. 나중에 워커노드들에

입력해줘야 하기 때문에 **잘 복사해두자**!!!

**++++ 여기서 또 굉장히 헤맸었다. kubeadm init 시 계속 에러가 났었다. 이는 docker, kubernetes를 초기화 하여 해결할 수 있다. 방법은 링크를 참고 하면 좋을 것 같다.([https://likefree.tistory.com/13](https://likefree.tistory.com/13))**

kubeadm init 에 대한 설명 참조 : [https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/)

[

kubeadm init

kubernetes.io



](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/)

**※ 삽질하며 깨닳은 것 하나 더..**

(+) 본 포스팅 이후, 서버에 실제 운영에 필요한 자원이 부족해져서 기존에 구축한 것을 한번 싹 밀고 단일 서버에 minikube를 설치했었다. 나중에 여유가 생겨서 다시 여러 호스트들을 이용한 클러스터링을 해보려했는데 잘 안된다..minikube를 설치하며 kubelet이 물고있던 설정값이 kubelet reset이나 지웠다가 다시 설치해도 swap메모리가 문제인건지 초기화가 안되서 그냥 서버를 지우고 다시 깔았다. ^\_\_^ 

(4) 환경변수 설정

root 계정을 이용해서 kubectl을 실행하기 위한 환경 변수를 설정한다.

이부분을 해주지 않으면 

"The connection to the server localhost:8080 was refused - did you specify the right host or port?"

라는 오류가 출력된다. 반드시 해줘야하는 부분이다.

(+ kubectl은 쿠버네티스 클러스터를 조작하기 위한 CLI 툴이다.)

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

---

**(7) Worker Node 설정**

(1) 도커 실행

```
systemctl daemon-reload
```

```
systemctl enable --now docker
```

(2) 쿠버네티스 실행

```
systemctl enable --now kubelet
```

(3) Node 연결

마스터에서 kubeadm init으로 나왔던 복사물을 붙여 넣는다.

(4) Node 연결 여부 확인

위 결과물의 끝 부분에서 말하는것 처럼 마스터 노드에서 아래와 같이 입력해보자.

```
kubectl get nodes
```

그러면 노드들이 연결된 모습을 출력해 준다...!!

※ 아직 Not Ready인 상태일 것이다. 통신 네트워크 연결을 하지 않았기때문에..!

++ 이부분에서도 상당한 삽질이 있었다. 처음 다른 버전의 쿠버네티스를 설치하고 다시 깔아서 그런지 "**Unable to connect to the server: x509....**" 에러가 발생했다. 이는 직접작성한 포스팅을 링크한다.

[https://zunoxi.tistory.com/43](https://zunoxi.tistory.com/43)

[

Server#9. 쿠버네티스 Unable to connect to the server: x509 에러

쿠버네티스의 마스터노드와 워커노드의 연결을 확인하려 $ kubectl get nodes 를 입력했을 시, Error saying "Unable to connect to the server: x509: certificate signed by unknown authority" 이런 에러가 뜨..

zunoxi.tistory.com



](https://zunoxi.tistory.com/43)

아래 명령어를 검색해보자.

```
kubectl get pods --all-namespaces
```

아마 coredns가 pending 상태일것이다. 이역시 통신 네트워크를 아직 연결하지 않았기 때문이다.

**일단은 여기까지 왔다면 클러스터링은 **정상적으로** 구축된것이다.** 이를 통신이 가능하게끔하고 가시화 시키기 위해서는 Calico와 Dashboard가 필요한데, 포스팅 서두에 기술했던것 처럼 쿠버네티스와 버전이 맞지 않으면 설치하기 꽤나 까다롭다.

---

Dashboard 띄워보겠다고 삽질했던 과정은 다음 포스팅에서 다뤄볼 예정. XD 

[##_Image|kage@VMY8K/btqDJgye8zz/kNg4wHr5hAazXE0dcjOrFK/img.png|alignCenter|data-filename="다운로드.png" data-origin-width="1211" data-origin-height="630" data-ke-mobilestyle="widthContent"|||_##]