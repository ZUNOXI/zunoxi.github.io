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


3. host-manager.log : Tomcat Host Manager Web app 로그 (가상호스트 매니저)


4. manager.log : Tomcat Manager Web App 로그 (웹콘솔)


5. localhost.log : host(특정 가상호스트 대상)한정 로그




### Log Handling

---

앞선 로그의 종류를 보면 일반적으로 catalina.out에 중요 로그파일이 모두 적재되는것을 알 수 있다.

이 catalina.out이 너무 커지면 몇만, 몇십만 이상의 문장이 적재되어 로그분석이 힘들고,

경우에 따라 100기가가 넘어가면 톰캣이 중지된다는 사례도 들었다. 

따라서 보기 좋게, 혹은 안정성을 위해 날짜별로 쪼개고 그 날짜가 오래된것은 지우는 과정이 필요하다.

지금까지 내가 알고있고, 또는 적용해본 로그 처리 방법은 2가지이다.


(1) 로그를 봐야하는 사용자 혹은 개발자가 `tail -f catalina.out` 으로 실시간 확인이 필요한경우 






### 참조글

- [`http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=11`](http://www.chlux.co.kr/bbs/board.php?bo_table=board02&wr_id=11)
- [`https://cassandra.tistory.com/5`](https://cassandra.tistory.com/5)