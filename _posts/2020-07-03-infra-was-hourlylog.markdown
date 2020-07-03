---
layout: post
title: Tomcat log 시간단위 분리  
subtitle: rotatelogs를 활용한 Tomcat log 시간단위 분리
categories: infra
tags: infra was
comments: true
published: true
header-img: img/infra/linux/memory/memory.jpg
---

## 개요

---
> 우리는 apache에서 제공하는 Rotatelogs를 사용함으로서 tomcat의 catalina.out 로그를 시간별로 분리 할 수 있다. 


나름의 삽질 끝에 답을 알아낸듯 하다.






### tomcat 로그 종류

---

1. catalina.out : 서버상에서 발생한 모든 내용(서버의 기동,정지,개시 등)을 기록한 파일이다. 

그렇기 때문에 생기는 로그의 양이 방대하다.

날짜별, 혹은 시간별로 로그를 분리하는것은 일반적으로 catalina.out에 사용한다.


2. catalina.yyyy-mm-dd.log : 톰캣에서 생기는 로그만을 기록한다.

Standard output(표준 스트림), Standard error(표준에러)의 로깅은 제외

host-manager.log


### 참조한 포스팅

- [`http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=11`](http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=11)
- [`https://cassandra.tistory.com/5`](https://cassandra.tistory.com/5)