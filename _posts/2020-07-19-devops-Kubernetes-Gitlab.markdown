---
layout: post
title: devops(2) Kubernetes에 Gitlab서버 설치
subtitle: jenkins를 활용한 파이프라인 구축
categories: devops
tags: devops Kubernetes
comments: true
published: true
header-img: img/devops/k8s/gitlab/logo.png
---

## 개요
> `Git push` 부터 쿠버네티스 파드 배포 까지 파이프라인 구축 두번째 단계
  
- 목차
	- [`DevOps구조`](#devops구조)
	- [`Helm?`](#helm)
	- [`Why Helm`](#why-helm-)
	- [`How to install`](#how-to-install-)
  
## Gitlab on k8s
---
필자는 프로젝트 소스의 형상관리 툴로서 git, 그중에서도 설치형으로 프라이빗하게 쓰기 위해 `gitlab`을 이용하는편인데, 실제 기업환경에서도 github보다는 gitlab을 많이 사용하는 것 같다. 왜인지는 모르겠지만 아무래도 _**설치형 서버라**_ 는 점과 _**프라이빗한 사용 환경**_ 에서도 비용이 청구되지 않는 등 여러 부분에서 메리트가 있지 않았을까 싶다. 이번 포스팅은 이런 특징이 있는 **`gitlab서버를 쿠버네티스상에 설치`** 하고 서비스로 배포해보려 한다.

<br>

쿠버네티스로 gitlab을 배포하는것은 gitlab 서버를 중단없이 이용할 수 있고, 로드밸런싱으로 부하분산할 수 있다는 것에 의미가 있지만, 사실 개인 혹은 소수의 집단이 사용하기에 일반 서버로컬에 설치하는것과 비교하면 큰 메리트가 있어보이지는 않는다. 그래도 궁극의 MSA 구현을 위해서는 gitlab자체도 쿠버네티스에서 클러스터링되어야 좋을것 같아 **`쿠버네티스 상에서 서비스 단위 배포를 해보려 한다.`**

<br>

위 설명은 아래 그림과 링크를 참고하면 왜 이렇게 구성을 하지?에 대한 의문을 해소할 수 있다.

![그림1](https://zunoxi.github.io/assets/img/devops/k8s/gitlab/1.png)
이미지 출처 : (https://developer.ibm.com/kr/journey/run-gitlab-kubernetes/)

참고링크 : [https://developer.ibm.com/kr/journey/run-gitlab-kubernetes/](https://developer.ibm.com/kr/journey/run-gitlab-kubernetes/)


<br><br>

**`(실습 전제조건)`**

1\. 쿠버네티스 설치 및 클러스터링 설정

2\. helm 2.x v 설치

---

**1\. persistent volume(퍼시스턴트 볼륨) 선언**

일반적으로 helm chart를 이용하여 gitlab의 커뮤니티 에디션(무료)인 gitlab-ce 서버를 설치한다면,

gitlab설정 데이터뿐만 아니라 postegres, redis에 대한 퍼시스턴트 볼륨 설정을 해줘야 한다.

```
$ vi pvgitlab.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-01
spec:
  capacity:
    storage: 20G
  accessModes:
  - ReadWriteOnce
  local:
     path: /data/data-volume/
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, operator: In, values: [k8s-node1]}

$ kubectl apply -f pvgitlab.yaml

#############################

$ vi pvpostgres.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-02
spec:
  capacity:
    storage: 20G
  accessModes:
  - ReadWriteOnce
  local:
     path: /data/data-volume/
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, operator: In, values: [k8s-node1]}

$ kubectl apply -f pvpostgres.yaml

#############################

$ vi pvredis.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-03
spec:
  capacity:
    storage: 20G
  accessModes:
  - ReadWriteOnce
  local:
     path: /data/data-volume/
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, operator: In, values: [k8s-node1]}
        
$ kubectl apply -f pvredis.yaml

#############################

$ vi pvetcgit.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-04
spec:
  capacity:
    storage: 20G
  accessModes:
  - ReadWriteOnce
  local:
     path: /data/data-volume/
  nodeAffinity:
    required:
      nodeSelectorTerms:
      - matchExpressions:
        - {key: kubernetes.io/hostname, operator: In, values: [k8s-node1]}
        
$ kubectl apply -f pvetcgit.yaml
```

※ 쿠버네티스 클러스터 환경의 node1(hostname : k8s-node1)에 /data/data-volume 폴더는 미리 생성했다.  
이 경우, 이름은 gitlab, redis 등 별도로 yaml 파일을 만들었지만 퍼시스턴트 볼륨 클레임에서 프로비저닝이 될 경우, 시스템별로 매칭 되지 않고 무작위로 바운딩될 것이다. 

아래 대시보드에서도 퍼시스턴트 볼륨이 생성된 것을 확인할 수 있다.

[##_Image|kage@yi5cE/btqGkQCynry/XRLi8b5C7AM8hwonzuo8Ak/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

---

**2\. gitlab 설치**

**2-1 ) helm chart 주입**

```
$ helm upgrade --install gitlab stable/gitlab-ce   --set global.edition=ce   --set certmanager-issuer.email=내 이메일주소   --set global.hosts.domain=내 ip주소

```

위 명령어를 입력했을 때 다음과 같은 에러가 발생할 수 있다.

[##_Image|kage@2VsDr/btqJijXo9bD/oDhKMVBXTEZK2O8Nh1vRAK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

잘 안보이니깐 에러 부분만 풀어서 적자면 이러하다.

```
Error: configmaps is forbidden: User "system:serviceaccount:kube-system:default" cannot list resource "configmaps" in API group "" in the namespace "kube-system"
Error: UPGRADE FAILED: configmaps is forbidden: User "system:serviceaccount:kube-system:default" cannot list resource "configmaps" in API group "" in the namespace "kube-system"
```

이는 **쿠버네티스의 권한 관리(RBCA) 문제 때문에** 발생한다. helm의 tiller가 쿠버네티스 클러스터의 api에 접근하여 영향력을 행세하기 위해서는 유효한 사용자인지 확인이 필요하다. 실제로 필자가 테스트 에러를 해결할 때는 꽤 고생했지만 역시나 스택갓버플로우(!)는 절대로 우리를 실망시키지 않는다 :) 해결방법은 다음 명령어들을 차례대로 입력해주면 된다.

```
$ kubectl create serviceaccount --namespace kube-system tiller
$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
$ kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'      
$ helm init --service-account tiller --upgrade
```

출처 : [https://stackoverflow.com/questions/46672523/helm-list-cannot-list-configmaps-in-the-namespace-kube-system](https://stackoverflow.com/questions/46672523/helm-list-cannot-list-configmaps-in-the-namespace-kube-system)

[

helm list : cannot list configmaps in the namespace "kube-system"

I have installed helm 2.6.2 on the kubernetes 8 cluster. helm init worked fine. but when I run helm list it giving this error. helm list Error: configmaps is forbidden: User "system:serviceaccoun...

stackoverflow.com



](https://stackoverflow.com/questions/46672523/helm-list-cannot-list-configmaps-in-the-namespace-kube-system)

위 작업을 마무리하고 다시 helm 명령어를 입력하면 아래의 모습을 볼 수 있다.

[##_Image|kage@d80ORc/btqGiqLgtYA/qPVsFBxQKv0oRbtE8YrRI0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

**2-2 ) 외부 접근 주소 설정**

```
$ helm upgrade gitlab stable/gitlab-ce       --set externalUrl=내 IP 주소(접근할 IP)

```

[##_Image|kage@bHG78v/btqGebPxcrJ/YfQUyy8ZjZKs5vHGuSFZyK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

---

**3\. 정상 설치 확인**

**3-1 ) pv 클레임의 정상 바운드 여부 확인**

퍼시스턴트 볼륨으로 요청한 클레임들이 모두 할당된 것을 알 수 있다.

[##_Image|kage@biCGES/btqGiqkcQz2/mEafzPyeDda64NIJiFWNOk/img.png|alignCenter|data-origin-width="1108" data-origin-height="279" data-ke-mobilestyle="widthContent"|||_##]

[##_Image|kage@dNGXpT/btqGebINUYV/p03pgTvMom4sDpcKC7ZUBk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

**3-2 ) 서비스 배포 확인**

원인은 알 수 없지만, gitlab-gitlab-ce는 계속 pending 상태인 것처럼 보인다.

[##_Image|kage@cAYjhs/btqJsjIehm3/NZn016uk1lsE936RdLcW4k/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

막상 서비스 내로 들어가 보면 파드는 정상적으로 배포 중인 것을 알 수 있다.

[##_Image|kage@skZFt/btqJphc5myz/rJnconML7VIQ5YRddvATEk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

그리고 해당 파드의 이벤트를 살펴보면 다음과 같다.

[##_Image|kage@bxw9Xz/btqJltZDr4a/HXo6SmJ5L8IOSYzb7gCrU1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

최초에는 "'Readiness probe failed: Get[http://172.16.36.71:80/](http://172.17.0.6:3003/): dial tcp [172.16.36.71:80](http://172.17.0.6:3003/): getsockopt: connection refused'." 라는 이벤트를 발생시키고, 파드의 상태 메시지도 이와 같이 출력되었다. 한 5~6번을 파드의 재생성이 반복되더니 갑자기 잘되었다... 원인을 알 수가 없다. 쿠버네티스 고수분들이 이 글을 본다면 원인을 알려줬으면 좋겠다..!

**3-3 ) Gitlab 서버 브라우저 접근 **

위 사진의 서비스의 내부 엔드포인트에서 볼 수 있듯이 HTTP 프로토콜의 80번 통신은 nodeport의 30776번으로 들어오게끔 맵핑되어있다.

[##_Image|kage@JiiKY/btqJls7v7VZ/BnGg5Y0kWtqhzz40YrjVu1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

이제 브라우저에서 해당 서버 ip:30776으로 들어가 보자.

[##_Image|kage@bfJsGQ/btqJnCIYTB4/Jhv9qMPOZpXvhmdK9sw6JK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

**설치형 Gitlab 서버를 쿠버네티스 서비스로 배포 중인 것을** 확인할 수 있다.

정말 엄청난 삽질을 거듭했고, 사실 지금도 이게 맞는 것인지 잘 모르고 사용하고 있지만, 우선 Gitlab을 쿠버네티스에서 돌릴 수는 있게 되었다. 추가적으로 CI/CD 파이프라인을 구축하며 업데이트해 나가려 한다.



