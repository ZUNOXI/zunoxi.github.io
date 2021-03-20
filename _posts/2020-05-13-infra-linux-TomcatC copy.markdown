---
layout: post
title: Server 2. CentOS7 tomcat 다중서버 구동하기
subtitle: CentOS7 tomcat 다중서버 구동하기
categories: infra
tags: infra server
comments: true
published: true
header-img: img/infra/server/hourly/tomcat.png
---

## 개요
> `Centos7` tomcat 다중서버 구동하
  
- 목차
	- [`웹 서버와 WAS 차이`](#웹-서버와-wasweb-application-server)
	- [`웹 서버와 WAS 연동`](#웹-서버와-was-연동)
  
## 다중 Was 구성
---
서버를 관리하다보면 하나의 서버에 로드밸런싱 구현이나 여러개의 서비스를 제공하기 위해 톰캣을 다중으로 구동하여 사용해야할 경우가 있다. 
이런 다중 톰캣을 띄워야 하는 경우에서의 환경설정을 알아보자.

<br>





참고 : CentOS7.5, Java 1.8, Apache Tomact 9.0.35 버전 환경에서 테스트한 포스팅이다.

<br>


---

**1\. tomcat 설치**

(나는 톰캣 9.x대 버전을 설치했다.)

톰캣 공식사이트에서 톰캣 9.0.35 버전의 링크를 복사한다.

[https://tomcat.apache.org/download-90.cgi](https://tomcat.apache.org/download-90.cgi)

(해당 페이지에서 core에 "apache-tomcat-9.0.35.tar.gz"에 오늘쪽 마우스를 눌러 링크를 복사한다.)

![그림1](/assets/img/infra/server/tomcatC/2.png)

**2\. 의존성 주입**

```
 yum install -y wget   
```

\* 참고 : GNU Wget는 웹  서버로부터  콘텐츠를  가져오는  컴퓨터프로그램으로, GNU 프로젝트의  일부. 

**3\. 톰캣 설치 및 압축풀기**

톰캣을 언패키징할 폴더로 이동하여 아래 명령어를 입력한다.

```
wget http://mirror.navercorp.com/apache/tomcat/tomcat-9/v9.0.35/bin/apache-tomcat-9.0.35.tar.gz
```

[##_Image|kage@6Ofq6/btqD7Ycw0WJ/tqutZXMDGcp2224Yay9mqk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

다음, 두개의 톰캣 폴더를 만든다. first, second로 나누어 만들었다.

[##_Image|kage@bNZLCt/btqD7X5MBCx/Pgb5per6cMofyoMny62bm1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

각 폴더에 압축파일을 복사 또는 이동하여 톰캣 파일 압축을 풀어준다.

```
tar xvzf apache-tomcat-9.0.35.tar.gz
```

[##_Image|kage@VP5si/btqD6REVT7d/BYMVjfCIPHBgem1tfXanL1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|톰캣파일 압축풀||_##]

원래 톰캣 하나만 설치하는경우는 여기서 환경변수를 설정해줘야 하나

우리는 두개 이상 설치 할 것이기 때문에 이후에 설정할 예정

**4\. catalina.sh 설정**

apache-tomcat-9.0.35/bin 폴더 이동

```
vi catalina.sh
```

[##_Image|kage@cfAUrR/btqD7XxXryA/kJL2tO3zK1uzjSrMr6hAa0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

드래그되어있는 부분을 각자 경로에 맞게 수정한다.(톰캣이 설치되어있는 root 경로라고 이해할 수 있다.

두번째 폴더에 있는 톰캣의 catalina.sh도 해당경로에 맞게 수정해준다.

**5\. server.xml 설정**

우리는 두개의 서버를 띄워야 하기때문에 기본서버의 포트번호를 변경해 줘야한다.

첫번째서버는 그대로 진행해도 될것이고, 두번째 서버는 아래 그림의 음영처리 된부분을

순서대로 **8105, 8180, 8109** 로 변경 할 것이다.

일반적으로 8005는 서버포트, 8009는 ajp 1.3 port, 8080은 Connector port 이다.

\*  참고 : AJP 프로토콜은 웹서버에서 받은요청을 WAS로 전달해주는 프로토콜이다.

[##_Image|kage@HnZby/btqD9inQqps/Dg1bkkcI4AXzUm9N5npMr0/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

[##_Image|kage@boxJbX/btqD8hiE7JH/PrmvL4s8upVVnpHHKfwdkK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

[##_Image|kage@cr8AEB/btqD6lzwXH4/QzaHbfk9QV4JkCqfyTwPMK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

**6\. Tomcat 실행**

톰캣의 bin폴더에 있는 startup.sh를 실행시킨다. 

(아래 명령어 입력)

```
/u01/test/first/apache-tomcat-9.0.35/bin/startup.sh
```

두번째 톰캣이 위치한곳에서도 경로를 바꿔주고 실행시킨다.

[##_Image|kage@oHvob/btqD9jAZcD9/9Za9XQXxcpoGNsKdQF8tSk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##][##_Image|kage@cb5gYx/btqD9ON6U4t/CMjHDsSlNEZfRnUTVt8c00/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

이런식으로 둘다 각자 환경변수 지정된 곳에서 잘 나오면 성공한것이다.

이제 다음 두가지 방법으로 정상적으로 포트가 개방되었는지 확인해보자.

**(1) 개방된 포트 확인**

```
netstat -nltp
```

[##_Image|kage@bTWRNy/btqD9u986EY/yxUiIflFvxZZsZTnViYOvk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

우리가 지정했던 포트들이 잘 개방되어 있는것을 알수 있다.

**(2) 브라우저에서 확인**

브라우저 주소창에서 확인해보자.

[##_Image|kage@Y93RN/btqEanvYyGH/omQXhvV8FWhikRLBxvNHCk/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|8080 포트로의 접속||_##]

[##_Image|kage@F8AGB/btqEalZbgDA/4rAX67VIY7JxxCBkCkh1A1/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|8081 포트로의 접재||_##]

이렇게 정상적으로 두개의 톰캣이 서로 다른 포트에서 돌아가고 있는것을 확인할 수 있다.

다중톰캣 설정 끄읏

(자동실행을 추가로 설정하고 싶다면 아래의 블로그를 참조)

[https://idchowto.com/?p=51853](https://idchowto.com/?p=51853)