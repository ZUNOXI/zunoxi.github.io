---
layout: post
title: Linux 6. 리눅스 su와 su - 의 차이
subtitle: Linux 6. 리눅스 su와 su - 의 차이
categories: infra
tags: infra linux/aix
comments: true
published: true
header-img: img/infra/linux/chown/logo.png
---

## 개요
> `리눅스(centos7)` 에서 `su와 su -` 의 차이
  
- 목차
	- [`su`](#su)
	- [`su -`](#su--)
  
## su 와 su -
---
얼마전 야간작업을하다 WAS를 재기동할 일이 있었는데, su와 su-의 차이를 정확하게 알지 못하여 다른계정의 환경변수를 사용하지 못해 애를먹었다.. 다음날이 되서야 원인을 확인했던 기억을 되짚어 글을 작성하려한다. 

<br>


### su

기본적으로 su는 **`Switch User`** 라는 뜻으로 기존 계정을 유지한 상태로 타계정에 로그인 하는것을 의미한다. 

예를들어 현재 root에서 작업중에 tomcat계정으로 들어가고 싶은경우 `su tomcat` 이런식으로 이동이 가능하다.

root에서 계정전환시에는 비밀번호가 필요없지만 tomcat에서 root로 전환시에는 비밀번호 입력이 필요하다.

또한, 보안상 root로의 접근은 허가거부된 상태일 수도 있다는점을 참고하면 좋을 것 같다.


<br>

### su -

그럼 여기서 su- 는 무엇일까, su - 도 su 명령어와 동일하게 사용자의 계정을 로그아웃하지 않은상태에서 전환하는것이라는것은 동일하다. 다만 전환할 사용자의 환경변수까지 가져올 수 있다는 특징이 있다.

<br>

예를들어 내가 현재 root계정으로 서버에 접속한다음 Jeus계정으로 전환하여 WAS(jeus)를 실행시켜야 하는경우, Jeus를 정상적으로 실행시키려면 환경변수 설정이 필요하므로 `su - jeus`를 사용해야한다. jeus계정에 환경변수를 설정해 놓았다는 가정이다.


<br>


필자는 바보같게도 jeus 운영이 미숙하여 su jeus로 전환 후 아무리 `jboot, jdown, ja` 명령어를 사용해도 환경변수가 없어 명령어가 실행되지않는 경험을 했다. 이 글을 보는 분들은 그런일이 없길 기원한다..
