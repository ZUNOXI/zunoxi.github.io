---
layout: post
title: JDBC, JNDI에 대한 이해
subtitle: jdbc, jndi, datasource, connection pool
categories: infra
tags: infra db
comments: true
published: true
---

## 개요

> `JDBC, JNDI` 가 무엇이고 datasource, connection pool에 대한 이해

-   목차
    
    -   [`JDBC란?`](#JDBC란 무엇인가?)
        
    -   [`Restful API?`](# Restful API?)
        
        [##_Image|kage@bbsb89/btqE4DK2J0J/RTd95su9yOKkmwUqKDwcJk/img.png|alignCenter|width="100%" data-origin-width="1200" data-origin-height="600" data-ke-mobilestyle="widthContent"|||_##]

# JDBC란?

---

RestAPI는 Representational State Transfer의 약자이며 클라이언트(웹브라우저, 모바일)가 필요한 자원이 있을때, 서버에게 요청하는 방식을 정의한 API 디자인이다.  
기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다.

-   HTTP Method인 POST, GET, PUT, DELETE를 통해 해당 자원에 대한 CRUD 매커니즘이 적용가능한 개념이라고 할 수 있다.
-   CRUD(Create:생성(POST), Read:조회(GET), Update:수정(PUT), Delete:삭제(DELETE))
