---
layout: post
title: Web 6. 스프링부트(Spring-boot) jar 파일 만들기
subtitle: 스프링부트(Spring-boot) jar 파일 만들기
categories: programming
tags: programming web
comments: true
published: true
header-img: img/dev/web/spring-jar/1.png
---

해당 포스팅은 [https://zunoxi.tistory.com/59?category=816528](https://zunoxi.tistory.com/59?category=816528)로 이전되었습니다.
<!--
## 개요
> `스프링부트(spring-boot)`프로젝트를 `jar`로 내보내기
  
- 목차
	- [`Spring boot`](#1-spring-boot)
	- [`jar format`](#2-jar-format)
	- [`pom.xml 설정`](#3-pomxml-설정)
	- [`jar build`](#4-jar-build)
  
## Spring-boot to jar
---
이번 포스팅에서는 Docker Container로 spring boot를 돌려보기 위한 사전 작업으로, 테스트를 위해 만든 spring boot 프로젝트를 jar 파일로 빌드해보려고 한다.

<br>

---

### **1\. Spring boot**

먼저, `legacy spring`이 아닌 `spring-boot`를 사용하는 이유는 말 그대로 테스트용이기 때문에 복잡한 로직을 수행할 일이 1도 없고, 따라서 별도의 WAS를 사용하지 않으려 한다. 내장 톰캣이면 충분하다고 판단했다.

(내가 생각하는 spring legacy와 spring boot의 가장 큰 차이점은 내장 톰캣을 쓰는지, 외부 WAS를 쓰는지 인 것 같다. 개발자가 아니라 시스템적인 관점에서 바라봐서 그럴수도 있다..)

<br>

---

### **2\. jar format**

앞서 기술했던것의 이유로 우리는 war파일이 아닌 `jar 파일로 프로젝트를 빌드`해야 한다. 물론 boot에서 외장 WAS를 쓸 수 있게끔 하는 설정이 있지만, 이 역시 테스트용이므로 jar로 빌드하고 내장 톰캣을 그대로 사용해보려 한다.

---

### **3\. pom.xml 설정**

<br>

![그림1](/assets/img/dev/web/spring-jar/2.png)

<br>

![그림2](/assets/img/dev/web/spring-jar/3.png)

<br>

글의 서두에 말한 것처럼 `docker image 제작용 프로젝트`를 만들예정이므로 이름은 'DockerTest'로 했다. 간단하게 메인 컨트롤러를 하나 만들어주고 pom.xml에 몇 가지 dependency를 추가했다. 추가한 dependency는 다음과 같다.

![그림3](/assets/img/dev/web/spring-jar/4.png)

<br>

pom.xml 파일 내용

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.2.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>DockerTest</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>
	<name>DockerTest</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		
		<!-- jsp jar -->
		<dependency>
			<groupId>org.apache.tomcat.embed</groupId>
			<artifactId>tomcat-embed-jasper</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger2</artifactId>
			<version>2.9.2</version>
		</dependency>
		<dependency>
			<groupId>io.springfox</groupId>
			<artifactId>springfox-swagger-ui</artifactId>
			<version>2.9.2</version>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```

위 코드에서 springfox에 관련된 내용은 RESTful API용으로 없어도 무방하다. 꼭 확인해야 할 부분은 <packaging> jar </packaging>으로 선언해야 한다는 것이다.

<br>

![그림4](/assets/img/dev/web/spring-jar/5.png)

MVC 패턴을 준수하여 구조를 만든 다음 index.html 파일과 application.properties의 설정을 마치고 난 후, Spring Boot App으로 실행하면

![그림5](/assets/img/dev/web/spring-jar/6.png)

<br>

![그림6](/assets/img/dev/web/spring-jar/7.png)

<br>

웹 브라우저를 통해, 테스트 중인 화면을 확인할 수 있다. 이제 jar파일로 만들어보자.

<br>

---

### **4\. jar build**

프로젝트 오른쪽 마우스 클릭 후 > Run As > Run Configurations를 클릭한다.

![그림7](/assets/img/dev/web/spring-jar/8.png)

`3 Mavne build`를 처음 클릭하는 경우에는 아래의 화면으로 바로 이동한다.

<br>

![그림8](/assets/img/dev/web/spring-jar/9.png)


해당 화면에서 왼쪽 탭 `Maven Build > jar build` 할 프로젝트 선택.

'Goal' 항목에 package라고 입력 후, profiles는 비워준다. (기본으로 pom.xml이라고 되어 있을 것이다.) 다음, build를 진행한다.

![그림9](/assets/img/dev/web/spring-jar/10.png)

`3.Maven build` 클릭

<br>

![그림10](/assets/img/dev/web/spring-jar/11.png)

정상적으로 빌드된 것을 확인 후, 프로젝트가 위치한 폴더의 target폴더로 이동한다.

<br>

![그림11](/assets/img/dev/web/spring-jar/12.png)

jar 파일이 빌드되었다.

이제 해당 화면에서 cmd창을 열고 jar파일을 실행시켜 주자.

```
$ java -jar DockerTest-0.0.1-SNAPSHOT.jar
```

![그림12](/assets/img/dev/web/spring-jar/13.png)

<br>

![그림13](/assets/img/dev/web/spring-jar/14.png)
페이지가 잘보인다.

웹브라우저로 정상 구동 여부를 확인할 수 있다.

<br>

> ※ 최초 프로젝트의 src/main/webapp에 html 파일을 넣고 구동 시에는 404가 출력되었는데, src/main에 html을 두거나 src/main/resources/static에 html을 두는 방법으로 해결했다. 혹시나 동일한 문제가 발생한 분은 해당 방법을 참고하면 될 것 같다.
