---
layout: post
title: Linux 1. 리눅스 CentOS7 Alias 설정하기
subtitle: 리눅스 CentOS7 Alias 설정하기
categories: infra
tags: infra linux/aix
comments: true
published: true
header-img: img/infra/linux/memory/memory.jpg
---

## 개요
> `Kubernetes` 클러스터에 `Dashboard`설치
  
- 목차
	- [`calico설치`](#1-calico-설치--dashboard-설치)
	- [`프록시서버 띄우기`](#2-proxy-서버-띄우기)
	- [`접속url이동`](#3-접속-url-입력브라우저의-주소창에)
  
## Dashboard for Kubernetes
---
kubernetes를 일반적인 유저가 커맨드라인이 아닌 가독성이 갖춰진 화면에서 작업을 하기위해선 쿠버네티스 Dashboard를 사용할 수 있다.

<br>


해당 포스팅은 아래의 글로부터 이어진다 :)

[https://zunoxi.github.io/devops/2020/04/27/devops-k8s-install-k8s/](https://zunoxi.github.io/devops/2020/04/27/devops-k8s-install-k8s/)



---

### **1\. Calico 설치 & Dashboard 설치**

쿠버네티스의 클러스터 네트워킹 Plug-in 중 하나인 `Calico` 설치

```
curl -O https://docs.projectcalico.org/v3.9/manifests/calico.yaml

sed s/192.168.0.0\\/16/172.16.0.0\\/16/g -i calico.yaml

kubectl apply -f calico.yaml
```

Calico의 관련 pod의 status가 running중인지 확인 (시간이 꽤 소요된다. 여러번 계속 입력해서 확인)

```
kubectl get pods --all-namespaces
```

Dashboard 설치

```
kubectl apply -f https://raw.githubusercontent.com/kubetm/kubetm.github.io/master/sample/practice/appendix/gcp-kubernetes-dashboard.yaml

```

---
### **2\. Proxy 서버 띄우기**

```
nohup kubectl proxy --port=8001 --address="본인 ip" --accept-hosts='^*$' >/dev/null 2>&1 &
```

\- nohup을 사용하는 이유는 **`백그라운드로 구동`** 시키기 위함이다.

---

### **3\. 접속 url 입력(브라우저의 주소창에)**

> http://"본인 ip":8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/.

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/devops/k8s/dashboard/1.png)

※ 추가 ++ 서버가 재기동되거나 프록시서버가 꺼져서 다시 dashboard를 띄워야 할때는

```
nohup kubectl proxy --port=8001 --address="본인 ip" --accept-hosts='^*$' >/dev/null 2>&1 &
```

요 작업만 다시 해주면 된다 😁