---
layout: post
title: html, css, javascript의 관계와 정의
subtitle: html, css, javascript에 대한 이해
categories: programming
tags: programming language
comments: true
published: true
header-img: img/dev/language/html/html.jpg
---

## 개요
> `html, css, javascript` 에 대한 이해
  
- 목차
	- [`DevOps구조`](#devops구조)
	- [`Helm?`](#helm)
	- [`Why Helm`](#why-helm-)
	- [`How to install`](#how-to-install-)
  
## 글의 목적
---
`html, css, javascript`에 대해 배우고 실제 여러 프로젝트에 적용해보며 느꼈던 것들을 정리한다.

<br>

(문법정리나 예제소스코드정리 포스팅이 아니며 링크의 [`포스팅`](https://developer.mozilla.org/ko/docs/Learn/JavaScript/First_steps/What_is_JavaScript)이 정리에 많은 도움이 되었다.)


### HTML
---

`html`(Hypertext Markup Language)는 프로그래밍 언어가 아니라 우리가 보는 웹페이지가 어떻게 구조화 되어있는지

브라우저로 하여금 알 수 있도록 하는 마크업 언어이다. 글, 이미지 등의 정적이 리소스들을 대상으로 한다.

(HTML 요소는 대소문자를 구분하지 않는다. 대게는 가독성을 위해 소문자로 작성한다.)



### CSS
---

배경색, 폰트 등 브라우저 보여지는 부분(프론트 엔드단)의 레이아웃을 지정하여 HTML 컨텐츠를 꾸며주는 언어.

나는 백엔드 개발보다 즉각적으로 확인이 가능하고 디자인 감각을 발휘할 수 있는 프론트엔드 개발이 더 재미있다.

그런면에서 CSS는 참 재미있는 언어이다.


### JS(JavaScript)
---

동적으로 컨텐츠를 바꾸고, 멀티미디어를 다루고 움직이는 이미지 등을 가능하게 하는 언어이다.

일반적인 웹서버가 html과 css로 이루어진 페이지를 구동할 수 있다면,

JavaScript로 짜여진 로직을 수행하기 위해서는 WAS(Web Application Server)가 필요하다.

> 사실 해당 포스팅을 쓰는 이유는 이 자바스크립트에 대해 정리하기 위함이다.

- `JavaScript`의 기능

    - 변수안에 값을 저장할 수 있다. html/css와 다르게 조건문, 함수들을 사용할 수 있는 프로그래밍언어이다.

    - 그러므로 웹 페이지상의 이벤트에 응답하는 코드를 작성할 수 있다.

    - 웹의 동적 처리를 담당한다. 콘텐츠를 바꾸고 움직이는 페이지의 동적 움직임을 가능하게 한다.


다음 [`링크`](https://html-css-js.com/)를 참고하여 html/css/javascript에 대해 쉽게 이해할 수 있다.