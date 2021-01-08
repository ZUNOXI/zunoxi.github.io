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
	- [`Alias`](#alias란)
  
## Alias for Linux

---

리눅스(Centos7.ver) 에서 다중 톰캣의 환경을 설정하다보니 안그래도 이름긴 톰캣의 경로를 매번 입력해서 들어가는것이 너무 귀찮았다. 그래서 이런 귀차니즘을 해결해 줄 수 있는 alias 를 활용해보기로했다.🤨 

<br>


---

### Alias란?


**`alias`** 는 특정 명령어를 애칭 혹은 별칭으로 저장하여 이를 간편하게 사용하게 하는 리눅스의 기능중 하나이다. 현재 필자가 운영중인 서버환경의 톰캣을 한번 실행시키려면 **/u01/test/second/apache-tomcat-9.0.35/bin** 위치까지 들어가서 실행시켜야한다.

자동실행을 사용하는 방법도 있긴하지만 기동 뿐만아니라 별도의 설정할때마다 위치로 찾아가는것은 귀찮다...그래서 alias를 사용하여 해당 위치로 바로 가게끔 해보려한다.

<br>

**1\. alias 설정확인**

일단, 현재 리눅스에 설정 되어있는 alias를 확인해 보자.

![그림1](/assets/img/infra/linux/alias/1.png)

사실 필자는 포스팅 이전 tomcat에 대한 alias 설정을 해놨었다.

<br>

**2\. alias 설정추가**

```
vi ~/.bashrc
```

명령창에 위의 스크립트를 입력한다.

![그림2](/assets/img/infra/linux/alias/2.png)

현재 설정되어 있는 alias 목록이 나온다. 나는 톰캣을 위해 **alias tomcat1='cd /u01/test/first/apache-tomcat-9.0.35'** 와 같이 설정했다.

이는 tomcat1을 쉘에 입력했을때 /u01/test/first/apache-tomcat-9.0.35의 `경로로 이동`하게하겠다는 뜻이다. 입력후에 저장을 한다. 이를 저장하지않으면 서버 재부팅시 초기화된다. 저장은 아래와 같이!

```
source ~/.bashrc
```

<br>

**3\. alias 사용 및 확인**

![그림3](/assets/img/infra/linux/alias/3.png)

tomcat1만 쉘에 입력했는데 해당 경로로 바로 이동된다.

alias를 사용하면 이러한 이동뿐만아니라 다양한 `사용자 명령어`를 만들 수 있다는 장점이있다.(물론 시스템명령어를 중복되게 입력하면 안된다..!)