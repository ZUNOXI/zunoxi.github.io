---
layout: post
title: Kubernetes 3. 쿠버네티스 Unable to connect to the server x509 에러
subtitle: 쿠버네티스 Unable to connect to the server x509 에러
categories: devops
tags: devops kubernetes
comments: true
published: true
header-img: img/devops/k8s/install/logo.png
---

해당 포스팅은 [https://zunoxi.tistory.com/43?category=950191](https://zunoxi.tistory.com/43?category=950191) 로 이전되었습니다.

<!--

## 개요
> 쿠버네티스 Unable to connect to the server x509 에러
  
- 목차
	- [`해결과정`](#해결과정)
  
## kubernetes x509 에러
---
쿠버네티스 클러스터 설치를 성공적으로 진행하던중 갑자기 노드들의 상태 확인이 불가한 딥빡의 상황이 펼쳐졌다.🤨

<br>


---

### 해결과정

쿠버네티스의 마스터노드와 워커노드의 연결을 확인하려  

```
$ kubectl get nodes 
```

를 입력했을 시,

**Error saying "Unable to connect to the server: x509: certificate signed by unknown authority"**

이런 에러가 뜨는 경우가 있다.

원인은 다양한것 같은데 필자가 참고해서 해결했던 방법은 아래와 같다.

```
export KUBECONFIG=/etc/kubernetes/admin.conf
```

출처 : [https://www.edureka.co/community/23959/unable-connect-server-certificate-signed-unknown-authority](https://www.edureka.co/community/23959/unable-connect-server-certificate-signed-unknown-authority)

위의 링크를 참고한다면 더 정확한 해답을 얻을수 있을것 같다.  🙃


-->
