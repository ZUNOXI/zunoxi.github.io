---
layout: post
title: JDBC에 대한 이해
subtitle: jdbc, jndi, dbcp, datasource, connection pool
categories: infra
tags: infra db
comments: true
published: true
header-img: img/infra/db/jdbc/db.jpg
---

## 개요
> `JDBC, JNDI` 와 datasource, connection pool에 대한 이해

-   목차
    - [`JDBC란?`]
    - [`DBCP`]       
    - [`JNDI`]


해당 [`포스팅`](https://eongeuni.tistory.com/43)이 내용을 정리하는데 큰 도움을 줬다.

### JDBC란?

---

`JDBC`는 Java DataBase Connectivity의 약자이며, 데이터베이스를 조작하는 API이다. 
JDBC, JNDI, DBCP 모두 JAVA에서 DB커넥션 할 때 사용하는 방법이다.

- 특징 : JDBC는 인터페이스기반으로 구축되어있다. 데이터베이스 커넥션 인터페이스라고 이해할 수 있다.
- 일반적인 JDBC는 데이터베이스풀 방식을 사용하지 않고 DB에서 정보를 가져올때마다 매번 디비연결을 열고 닫는다.
- 따라서 상용 어플에는 JDBC 방식을 사용하는 경우가 거의 없다. 매우 비효율적이기 때문이다.


### DBCP

---

그래서 일반적인 경우에는 Pool 방식을 사용한다.

여기서 등장하는 용어가 `DBCP`(Database Connection Pool)이다.

- 특징: 어플리케이션을 시작할 때 원하는 만큼 커넥션 객체를 만들어 놓고  pool에 넣어 놓은채 필요할때마다 가져다 쓰고 다시 만납하는 방식이다.
- 다중스레드를 스레드풀로 관리하는것과 비슷한 방식이라고 한다.
- DBCP는 보통 데이터베이스 커넥션 풀을 어플리케이션 소스단에 설정해놓은 방식이다.



### JNDI

---

`JNDI`(Java Naming and Directory Interface)는 WAS단에 데이터베이스 커넥션 객체를 미리 네이밍 해두는 방식이다.

사전적으로는 디렉토리 서비스에서 제공하는 데이터 및 객체를 발견하고 참고하기 위한 자바 API 라고 한다.

- 특징 : DB커넥션을 WAS단에서 제어하면서 서버에서 하나의 커넥션 풀을 가진다. 공유객체를 사용한다고 이해할 수 있다.
	- 장점 1 : DB 설정 정보를 파악하기 쉽다 > WAS단에 설정정보를 통해 디비가 몇개 붙어있는지 정보를 파악하기 수월하다.
    - 장점 2 : DB 커넥션풀을 효율적으로 사용할 수 있다. 
    (WAS단에서 DB Pool을 하나로 관리하면 스태틱 객체를 생성 후에 쉽게 가져다 쓸 수 있기때문에 효율이 좋아진다.) 



각 방법마다 장단점이 있기 때문에 무조건 좋은것은 없다.
