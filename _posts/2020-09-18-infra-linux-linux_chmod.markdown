---
layout: post
title: Linux 4. 리눅스 접근권한관리(chmod,chown)
subtitle: Linux 4. 리눅스 접근권한 관리 / chmod, chown
categories: infra
tags: infra linux/aix
comments: true
published: true
header-img: img/infra/linux/chown/logo.png
---

## 개요
> `리눅스(centos7)` 에서 `chmod,chown` 를 통해 접근권한을 관리
  
- 목차
	- [`리눅스 접근권한`](#1-리눅스-접근권한)
	- [`접근권한 부여`](#2-접근권한-부여)
	- [`접근 유저, 그룹 변경`](#3-접근-유저-그룹-변경)
	- [`umask`](#4-umask)
  
## 리눅스 접근권한 관리의 필요성
---
인프라를 관리하며 리눅스를 일반적으로 서버로 많이 사용하다 보니 불특정다수에대한 접근권한을 관리할일이 많다. 이번 포스팅에서는 파일, 디렉토리에 대한 리눅스의 접근권한 관리 방법에 대해 다뤄보고 정리해보려 한다.😌

<br>




---

### **1. 리눅스 접근권한**

<br>

리눅스에서 기본적으로 파일에 대한 접근권한은 `666`이며 폴더는 `777`이다. 접근 권한은 r, w, x로 표현하는데 폴더, 혹은 파일의 접근권한은 리눅스 쉘에 '**ls -l(or ll)**'을 입력하여야 확인할 수 있다.

<br>

![그림1](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/chown/1.png)

왼쪽 빨간 박스로 표시가 된 것이 접근권한이다. 가장 위에 있는 'bin' 폴더의 접근 권한에 대해 알아보자. '**dr-xr-xr-x**' : 여기서 d는 디렉토리를 의미하며 r,w,x에 대해서 설명하면 **r : 읽기 권한, w : 쓰기 권한, x : 실행 권한이라고** 이해할 수 있다.

<br>

그리고 가장 앞글자인 d 혹은 l 을 제외한 나머지 9개의 글자는 3개씩 끊어서 해석한다. 즉, **'r-x, r-x, r-x'** 라고 구분하고 각각 앞에서부터 **User의 권한, Group권한, Others 권한**으로 분류한다.

이는 해당 파일이나 폴더를 만든 사람을 기준으로 해당 사용자(user)의 권한, 그리고 해당 사용자가 속한 그룹(Group)의 권한, 그 외의 접근하는 타 계정에(Others) 대한 권한으로 나뉜다. (여기서 root는 그 어떤 사용자가 만든 파일과 폴더에도 접근할 수가 있다.)

<br>

접근권한은 보통 r : 4, w : 2, x : 1 이진법으로 표현하고 각 권한을 더해서 계산한다. 예를 들어 "root가 만든 특정 파일의 접근권한이 **754이다."라는** 것은 root는 읽기, 쓰기, 실행 권한을 모두 갖는 `7(4+2+1)을 의미`하고, root가 속한 그룹은 읽기와 실행 권한을 갖는 5(4+1), 그리고 그 외의 사용자는 읽기만 가능한 4를 의미한다. 사실 이 부분만 잘 이해한다면 그렇게 어렵지 않은 부분인 것 같다.

<br>

---

### **2. 접근권한 부여**

<br>

그럼 `기존에 접근권한이 부여된 파일이나 폴더`의 권한은 어떻게 수정할 수 있을까? 이는 **chmod**를 이용해서 수정할 수 있고 다음과 같이 사용하면 된다.

```
$ chmod 755 /u01/infra/test.sh
```

위 명령어는 /u01/infra에 있는 test.sh라는 파일에 **755 권한을** 부여해준다는 뜻이다. 755 권한은 위에 설명했던 내용으로 확인할 수 있을 것이다. (여기서 하위 폴더까지 권한을 동일하게 주고 싶다면 **chmod -R** ~~~ 로 사용하면 된다.)

<br>

---

### **3\. 접근 유저, 그룹 변경**

<br>

더 나아가서 권한뿐만 아니라 `유저와 그룹에 대한 설정`도 변경해줄 수 있다. 먼저 해당 폴더의 사용자, 그룹별 권한을 확인해보려면 위와 마찬가지로 리눅스 쉘에 '**ls -l(or ll)'** 을 입력한다. 아래 사진처럼 root, root라고 확인된 부분이 그것인데 이는 **유저 권한(왼쪽) : root, 그룹 권한(오른쪽) : root**는 사실상 해당 파일에 대해 일반 유저는 Others 권한에 해당되는 것만 접근이 가능하다.

<br>

![그림2](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/chown/2.png)

여기서 권한을 변경할 때는 chown을 사용한다.

```
$ chown ituser:itteam /u01/infra/test.sh
```

위 명령어는 /u01/infra/test.sh 라는 파일을 사용자 권한은 ituser, 그룹 권한은 itteam으로 바꿔준다는 뜻이다. 물론 해당 명령어를 사용하는 계정은 해당 파일에 대한 쓰기 권한을 갖고 있어야 하며 ituser는 itteam에 속해있어야 한다. chown에서 하위 폴더 역시 모두 권한을 변경해주는 방법은 **chown -R** ~~~ 옵션을 사용해주면 된다.

<br>

---

### **4\. umask**

<br>

여기서 또 `Umask`라는 게 있는데, Umask는 현재 리눅스에 접속한 계정이 기본적으로 파일을 생성할 때의 갖는 기본 권한이라고 할 수 있다. 예를 들어 리눅스 쉘에 umask라는 명령어를 입력했을 때 

![그림3](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/chown/3.png)

**0022**라고 나오는 것이 일반적인 폴더, 파일 생성 권한이며 폴더는 **777 - umask값**(뒤에서 세 자리), 파일은 **666에서 -umask값**(뒤에서 세 자리)으로 계산한다. 즉, `0022` 같은 경우, 폴더는 777-022 = 755 권한, 파일은 666-022 = 644 권한으로 생성된다. 아래 사진에서 이를 확인할 수 있다. 

![그림4](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/chown/4.png)

<br>

![그림5](https://cdn.jsdelivr.net/gh/zunoxi/zunoxi.github.io/assets/img/infra/linux/chown/5.png)

이렇게 리눅스의 기본적인 접근권한과 접근권한 부여 및 변경에 대해 알아봤다.

> 포스팅에 잘못된 부분이나 추가되었으면 좋을 부분은 지적해주시면 적극 반영하겠습니다 😌


