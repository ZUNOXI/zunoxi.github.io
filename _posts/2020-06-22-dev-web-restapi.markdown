---
layout: post
title: Rest API에 대한 이해
subtitle: rest api와 restful api
categories: programming
tags: web
comments: true
published: true
---

## 개요
> `RestAPI` 가 무엇이고 어떻게 사용해야하는가?
  
- 목차
	- [`RestAPI란 무엇인가`](#RestAPI란 무엇인가?) 
	- [`Restful API?`](# Restful API?)
  
## RestAPI란 무엇인가?
---
RestAPI는 Representational State Transfer의 약자이며 클라이언트(웹브라우저, 모바일)가 필요한 자원이 있을때, 서버에게 요청하는 방식을 정의한 API 디자인이다.
기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다.

- HTTP Method인 POST, GET, PUT, DELETE를 통해 해당 자원에 대한 CRUD 매커니즘이 적용가능한 개념이라고 할 수 있다.
- CRUD(Create:생성(POST), Read:조회(GET), Update:수정(PUT), Delete:삭제(DELETE))

## Restful API?   
---
Restful API에 대한것은 명확하게 정의내려진것이 없으며, 일반적으로 REST 구조를 만족시키는 포맷을 Restful 하다고 이야기한다. 

## 간단한 RestAPI 서버 만들기
---
> spring 서버 설계
server 구성은 spring-boot와 mybatis(pom.xml에 의존성 주입필요)를 사용할 예정.
(구조는 MVC 형태로 설계)

1. controller 생성
![그림1](https://zunoxi.github.io/assets/img/dev/web/restapi/spring1.jpg)

2. service 생성
![그림2](https://zunoxi.github.io/assets/img/dev/web/restapi/service.jpg)

3. repository 생성
![그림3](https://zunoxi.github.io/assets/img/dev/web/restapi/repo.jpg)

4. dto 생성
![그림4](https://zunoxi.github.io/assets/img/dev/web/restapi/dto.jpg)

5. mybatis와 mysql 연동
![그림5](https://zunoxi.github.io/assets/img/dev/web/restapi/mybatis.jpg)

![그림6](https://zunoxi.github.io/assets/img/dev/web/restapi/sql.jpg)

## RestAPI 구조 확인
---

1. Front-end 개발단에서의 Restapi 직접 활용해보기
> `vue`를 이용해서 입력한 데이터를 바인딩해서 서버단으로 전송하게 구성
![그림7](https://zunoxi.github.io/assets/img/dev/web/restapi/vscode.png)

만들어진 프론트엔드 웹브라우저상에서 데이터를 입력
![그림8](https://zunoxi.github.io/assets/img/dev/web/restapi/vue.jpg)

서버의 콘솔로그에 데이터가 정상적으로 들어온것을 확인
![그림9](https://zunoxi.github.io/assets/img/dev/web/restapi/console.jpg)

2. Swagger를 활용한 Api-Document 확인

> swagger-ui를 통해 api spec 확인
(pom.xml에 swagger 의존성 주입 필요)

![그림10](https://zunoxi.github.io/assets/img/dev/web/restapi/swagger.jpg)

POST 요청으로 테스트해볼 데이터내용을 입력후 execute 
![그림11](https://zunoxi.github.io/assets/img/dev/web/restapi/swagger2.jpg)

서버 콘솔에 데이터가 들어오는것을 확인
![그림12](https://zunoxi.github.io/assets/img/dev/web/restapi/console2.jpg)

restapi에 대한 정리.
