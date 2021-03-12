---
layout: post
title: Kubernetes 2. CentOS7에 쿠버네티스 설치하기
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
	- [`1.서버 가상화`](#1-서버-가상화)
	- [`2 VM Centos 설치`](#2-vm-centos를-설치)
	- [`3.VM 세팅`](#3-vm-세팅)
	- [`4.Docker와 Kubernetes 설치`](#4-docker와-kubernetes-설치)
  
## Kubernetes install on Centos7
---
리눅스에 대한 기초가 부족해서 거의 1주일이 넘게 쿠버네티스 설치에 고구마 1,000개 정도 먹은 것 같았는데, 마침내... 쿠버네티스 대시보드에 진입하게 되어 세상 기쁩니다.😎 필자처럼 고생하는 분들이 없길 바라며 설치했던 과정을 포스팅해보려 합니다.

<br><br>



![그림1](/assets/img/devops/k8s/install/1.png)

(찬란한 대시보드..😇)

---

본 포스팅은 현재 `쿠버네티스 강의`로 듣고 있는 **김태민 님의 인프런강의**를 참고하며 포스팅을 작성했습니다. 쿠버네티스관련 국내 강의가 별로 없는데 쿠린이 입장에서는 환경구축에 큰 도움이 된 것 같습니다. 개인적으로 쿠버네티스를 **온프레미스** 환경에 구축하려고 하는 사람들에게 적극 추천하고 싶은 강의입니다. 

<br>

단, 자세한 설명으로 구성된 강의이지만 필자가 사용하는 서버 OS 버전이 달랐었고 각자의 pc 환경이 다르다 보니 별도로 수정해야 할 것들도 일부 발생 할 수 있다는점을 참고하면 좋을 것 같습니다.


<br>

**`※ 해당 포스팅은 처음 쿠버네티스 클러스터를 구성해보는 사람들을 위한 실습 과정을 기술했습니다. 실무에서 사용할 만큼의 규모 있는 쿠버네티스 클러스터 구성과는 거리가 있을 수 있습니다.`**

<br>

- 참고했던 김태민님의 블로그와 CUBRID 블로그

	[https://kubetm.github.io/practice/appendix/installation\_case1/](https://kubetm.github.io/practice/appendix/installation_case1/)

	[https://www.cubrid.com/blog/3820603](https://www.cubrid.com/blog/3820603)

<br>

해당 포스팅의 실습에서는 개인용 노트북(or 데스크탑) + VM 조합이 아닌 vSphere를 이용한 몇대의 호스트 서버가 클러스터링 되어있는 환경에 VM을 3개 생성후 설치를 진행했습니다.

> _작업환경(OS) : CentOS Linux release 7.7.1908 (Core)_

<br>

---

### **1. 서버 가상화**

<br>

기본적으로 VM을 만들어 실습할 `마스터 노드 1개(메인 노드)`와 `미니언 노드(워커 노드) 2개`가 필요합니다. 이때, 한 개의 서버에 공통된 설정을 하고 vm 여러 개를 복사하는 방법이 더 효율적이기 때문에, 먼저 **`공통설정을 완료한 후 vm이미지 자체를 복사`** 하는 것이 좋습니다. 

**virt-Manager와 kvm**을 이용하면 `리눅스OS위에 가상머신`을 만들 수 있습니다. 단, 여기서 본인의 PC나 노트북이 가상화 기능을 지원하는지 확인이 필요합니다. 가상화 기능을 지원하지 않는다면 여러 VM을 띄울수 없기때문에(다수vm 클러스터링 불가) **minikube**를 이용해야 합니다. 관련해서는 아래 링크를 참고하면 좋을 것 같습니다.

[https://kubernetes.io/ko/docs/tasks/tools/install-minikube/](https://kubernetes.io/ko/docs/tasks/tools/install-minikube/)

<br>

필자는 앞서 말한것 처럼 vSphere를 사용해서 VM을 3개 생성하고 이를 클러스터링 작업했습니다. 이와 별개로 개인 리눅스 PC에서 가상화 시키는 부분은 구글에 많은 방법이 있으니 각 pc의 사정대로 맞춰서 설치하면 될 것 같습니다.

<br>

---

### **2. VM Centos를 설치**

앞선 과정을 통해 총 3개의 가상서버내 OS 설치를 할 차례입니다. 여기서 **Centos7**을 운영체제로 선택하여 설치해 줍시다. 개인 PC에서 vm을 이용해서 실습하는 경우에는 다운 받은 centos7을 직접 VMware나 VirtualBox를 사용해 밀어넣어 설치할 수 있습니다.

`centos7은 아래 링크에서 다운로드` 받을 수 있다.

[http://isoredirect.centos.org/centos/7/isos/x86\_64/](http://isoredirect.centos.org/centos/7/isos/x86_64/)

<br>

VM에 centos7 설치하는것 까지 IT 종사자라면 대부분 수월하게 할 수 있을것이라 생각합니다. (물론 저는 IT종사자이지만 수월하지 않았다고합니다..;;) 이제 이 다음부터가 쿠버네티스 설정에 중요한 단계라고 할 수 있을것 같으니, `단계별로 정확히 설치 및 적용`하고 넘어가야합니다.

<br>

---

### **3. VM 세팅**

<br>

> (1) Selinux 설정 

<br>

**selinux**는 미국 국방부 스타일의 강제 접근 제어(MAC)를 포함한 접근 제어 보안 정책을 지원하는 `리눅스 커널 보안 모듈`입니다.
(보안 강화 리눅스(Security-Enhanced Linux)의 약자)

쿠버네티스에서는 Pod Network에 필요한 호스트 파일 시스템에 액세스가 가능하게 하는 설정이라고 합니다.

```
vi /etc/sysconfig/selinux
```

**SELINUX=disabled** 을 **SELINUX=permissive** 로 변경후 저장합니다. `위 설정은 서버를 reboot 해야 적용됩니다`

```
sestatus   # 이 명령어로 상태를 확인
```

<br>

> (2) 방화벽 해제

<br>

firewalld 비활성화 (CentOS 7의 방화벽 관리 데몬은 firewalld 입니다.)

```
systemctl stop firewalld && systemctl disable firewalld
```

NetworkManager 비활성화

```
systemctl stop NetworkManager && systemctl disable NetworkManager
```

<br>

> (3) SWAP 비활성화

```
swapoff -a && sed -i '/ swap / s/^/#/' /etc/fstab
```

<br>

> (4) Iptables 커널 옵션 활성화

<br>

Centos7 사용 시 iptables가 무시되서 트래픽이 잘못 라우팅 되는 문제가 발생한다고 합니다. 

```
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
<br>

> (5) 쿠버네티스 YUM Repository 설정

<br>

`yum`은 Centos7의 패키지 관리 시스템이며 파이썬으로 설계되어있습니다.

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
<br>

> (6) Centos Package Update

<br>

```
yum update
```
<br>

> (7) Hosts 등록

<br>

호스트 미설정 시 kubeadm init에서 Host이름으로 IP를 찾을 수 없다는 에러가 발생할 수 있습니다. 여기서 `Kubeadm`은 쿠버네티스 클러스터의 가장 빠른 설치를 도와주는 쿠버네티스 기본 구성요소입니다.

(참고 : [https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/))

```
cat << EOF >> /etc/hosts
[master의 IP] k8s-master
[node1의 IP] k8s-node1
[node2의 IP] k8s-node2
EOF
```

<br>

---

### **4. Docker와 Kubernetes 설치**

<br>

> (1) 도커 설치 전 사전 세팅


```
yum install -y yum-utils device-mapper-persistent-data lvm2 
```
<br>

>(2) 도커 저장소 설정

```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
<br>

>(3) 도커 패키지 설치

```
yum update && yum install docker-ce-18.06.2.ce
```
<br>

>(4) 쿠버네티스 설치

```
yum install -y --disableexcludes=kubernetes kubeadm-1.15.5-0.x86_64 kubectl-1.15.5-0.x86_64 kubelet-1.15.5-0.x86_64
```

_**※ 여기서 중요한것은 본 포스팅대로 설치 진행시 쿠버네티스 관련설정을 `1.15 버전`으로 설치해야한다는 것**_

처음에는 현재최신버전보다 낮다고 뜨길래 1.18 버전으로 변경해서 설치했다가 대시보드 1.x버전대가 맞지 않아서 고생을 좀 많이했습니다... 현재 확인결과, `쿠버네티스 1.16 이상버전은 대시보드 2.0을 사용해야 한다고합니다.`

(김태민님 강의의 Q&A에서 알게된것..!)

대시보드 2.0에서는 보안이 강화되어 토큰이나 `kuberconfig 같은 인증`을해야하는데, 그러기 위해서는 kubernetes-dashboard namespace를 수정해야합니다. 근데 왜 제가 할때는 또 안되는지 모르겠네요...방법을 고민하다가 아예 그냥 `쿠버 1.15 버전 + 대시보드 1.0 버전`으로 다운그레이드 해서 진행했습니다. 

(쿠버 1.6이상의 버전에서 대시보드를 사용하시는 분은 다음 링크를 참조하면 좋을것 같습니다.)

링크 : [https://github.com/kubernetes/dashboard](https://github.com/kubernetes/dashboard)

<br>

---

### **5. VM clone**

<br>

VM을 복사해서 진행하는 분들은 이단계에서 `원하는 워커노드의 개수만큼 VM을 복사`해주면 되겠습니다. 서두에 기술했던 것 처럼 직접 미리 VM을 만들고 따로따로 다 설치하는것은 번거롭기 때문에 위에 설명한것처럼 기본설정이 끝난 후 VM복제를 추천합니다.

이를 강조하는 이유는, 필자는 가상서버 3개를 미리 만들어 놓고 테스트를 했었기에(눈물😢) vm clone은 하지 않고 그냥 3번씩 반복해서 node(워커노드) 2개에 동일한 환경을 만들어줬었기 때문입니다. 이글을 읽는 분들은 제발 이런 비효율을 경험하지 않았으면 좋겠습니다...

<br>

---

### **6. Master node 설정**

<br>

> (1) 도커 실행

```
systemctl daemon-reload
```

```
systemctl enable --now docker
```

> (2) 쿠버네티스 실행

```
systemctl enable --now kubelet
```

> (3) 쿠버네티스 초기화 실행

kubeadm init을 사용하여 쿠버네티스 클러스터를 빠르게 구축해 줍니다.

```
kubeadm init --pod-network-cidr=172.16.0.0/16
```

" 위 명령어를 실행하면 "kubeadm join xxx.xxx.xxx.xxx....." 라는 메세지출력됩니다. 나중에 워커노드들에

입력해줘야 하기 때문에 **잘 복사해둡시다**!!!

<br>

**※ 필자는 여기서 또 굉장히 헤맸습니다. kubeadm init 시 계속 기존 설정이 남아있다는 메시지와 함께 에러가 났습니다. 확인결과, 이는 docker, kubernetes를 초기화 하여 해결할 수 있습니다. 방법은 링크를 참고 하면 좋을 것 같습니다.([https://likefree.tistory.com/13](https://likefree.tistory.com/13))**

<br>

kubeadm init 에 대한 설명 참조 : [https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/](https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm-init/)

<br>

**`※ 삽질하며 깨닳은 것 하나 더.`**

(+) 본 포스팅 이후, 서버에 실제 운영에 필요한 자원이 부족해져서 기존에 구축한 것을 한번 싹 밀고 단일 서버에 minikube를 설치했었습니다. 나중에 여유가 생겨서 다시 여러 호스트들을 이용한 클러스터링을 해보려했는데 이게 또 잘안되는 현상이..

minikube를 설치하며 kubelet이 갖고있던 설정값이 kubelet reset이나 관련 패키지를 지웠다가 다시 설치해도 swap메모리가 문제인건지 초기화가 안되서 그냥 서버를 지우고 다시 깔았습니다. ^_^ 
애매하다 싶으면 빠르게 재설치하는것도 방법인것 같기도합니다..

<br>

> (4) 환경변수 설정

root 계정을 이용해서 kubectl을 실행하기 위한 환경 변수를 설정합니다.

이부분을 해주지 않으면 

`"The connection to the server localhost:8080 was refused - did you specify the right host or port?"`

와 같은 오류가 출력됩니다. 반드시 해줘야하는 부분입니다.

(+ `kubectl`은 쿠버네티스 클러스터를 조작하기 위한 CLI 툴.)

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

<br>

---

### **(7) Worker Node 설정**

<br>

> (1) 도커 실행

```
systemctl daemon-reload
```

```
systemctl enable --now docker
```

> (2) 쿠버네티스 실행

```
systemctl enable --now kubelet
```

> (3) Node 연결

마스터에서 kubeadm init으로 나왔던 복사물을 붙여 넣습니다.

> (4) Node 연결 여부 확인

위 결과물의 끝 부분에서 말하는것 처럼 마스터 노드에서 아래와 같이 입력해봅시다.

```
kubectl get nodes
```

그러면 노드들이 연결된 모습을 출력해 줍니다...!!

※ 아직 Not Ready인 상태일 것입니다. `통신 네트워크 연결`을 하지 않았기때문에..!

++ 이부분에서도 상당한 삽질이 있었습니다. 처음 다른 버전의 쿠버네티스를 설치하고 다시 깔아서 그런지 "**Unable to connect to the server: x509....**" 에러가 발생했었습다. 이는 직접작성한 포스팅을 링크합니다.

[https://zunoxi.github.io/devops/2020/04/28/devops-k8s-x509error/](https://zunoxi.github.io/devops/2020/04/28/devops-k8s-x509error/)


**자, 다음 아래 명령어를 입력해봅시다**

```
kubectl get pods --all-namespaces
```

아마 `coredns가 pending` 상태일 것입니다. 이 역시도 `통신 네트워크를 아직 연결하지 않았기 때문`입니다.

<br>

일단은 여기까지 왔다면 통신여부와 상관없이 클러스터링으로 연결은 **정상적으로 구축된것 입니다.** 이를 통신이 가능하게끔하고 가시화 시키기 위해서는 Calico와 Dashboard가 필요한데, 포스팅 서두에 기술했던것 처럼 쿠버네티스와 버전이 맞지 않으면 설치하기 꽤나 까다롭습니다.

---

Dashboard 띄워보겠다고 삽질했던 과정은 다음 포스팅에서 다뤄볼 예정. XD 

![그림2](/assets/img/devops/k8s/install/logo.png)


> 다음포스팅 : [Kubernetes 4. 쿠버네티스 네트워크/Dashboard 설정하기](#https://zunoxi.github.io/devops/2020/05/08/devops-k8s-dashboard/)