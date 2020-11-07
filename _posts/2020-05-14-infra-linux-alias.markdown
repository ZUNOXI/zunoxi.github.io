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
> `Linux`에 `Alias`설정하기
  
- 목차
	- [`calico설치`](#1-calico-설치--dashboard-설치)
	- [`프록시서버 띄우기`](#2-proxy-서버-띄우기)
	- [`접속url이동`](#3-접속-url-입력브라우저의-주소창에)
  
## Alias for Linux

---

리눅스(Centos7.ver) 에서 다중 톰캣의 환경을 설정하다보니 안그래도 이름긴 톰캣의 경로를 매번 입력해서 들어가는것이 너무 귀찮았다. 그래서 이런 귀차니즘을 해결해 줄 수 있는 alias 를 활용해보기로했다.🤨 

<br>


---

" **alias**는 특정 명령어를 애칭 혹은 별칭으로 저장하여 이를 간편하게 사용하게 하는 리눅스의 기능중 하나이다."

현재 톰캣을 한번 실행시키려면 **/u01/test/second/apache-tomcat-9.0.35/bin** 위치까지 들어가서 실행시켜야한다.

(사실 자동실행을 사용하면 간편하긴하지만 기동 뿐만아니라 별도의 설정할때마다 위치로 찾아가는것은 귀찮다...)

alias를 사용하여 해당 위치로 바로 가게끔 해보겠다.

**1\. alias 설정확인**

일단, 현재 리눅스에 설정 되어있는 alias를 확인해 보자.

[##_Image|kage@dcn6xZ/btqD8hKtEWx/MActekx4FWKr8DBkbKZoa0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

사실 나는 포스팅 이전 tomcat에 대한 alias 설정을 해놨었다.

**2\. alias 설정추가**

```
vi ~/.bashrc
```

명령창에 위의 스크립트를 입력한다.

[##_Image|kage@cGSn4O/btqEbb9FO4A/6MzAJVzq5GZ7Nr81BrZgK1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|\\||_##]

현재 설정되어 있는 alias 목록이 나온다.

나는 톰캣을 위해 " **alias tomcat1='cd /u01/test/first/apache-tomcat-9.0.35'** "와 같이 설정했다.

이는 tomcat1을 쉘에 입력했을때 /u01/test/first/apache-tomcat-9.0.35의 경로로 이동하게하겠다는 뜻이다.

입력후에 저장을 한다.

저장은 아래와 같이!

```
source ~/.bashrc
```

**3\. alias 사용 및 확인**

[##_Image|kage@bDzpwC/btqD7HCXPzO/kf7OHh6vbztPQMNLcmukD1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

tomcat1만 쉘에 입력했는데 해당 경로로 바로 이동된다.

alias를 사용하면 이러한 이동뿐만아니라 다양한 사용자 명령어를 만들 수 있다는 장점이있다.

(물론 시스템명령어를 중복되게 입력하면 안된다..!)