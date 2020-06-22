---
layout: post
title:  "Rest API에 대한 이해"
subtitle:   "rest api와 restful api"
categories: web
tags: web
comments: true
---

## 개요
> `RestAPI` 가 무엇이고 어떻게 사용해야하는가?
  
- 목차
	- [`RestAPI란 무엇인가`](#RestAPI란 무엇인가?) 
	- [`Restful API?`](#r설치window-pc-버전)
	- [R Studio설치(Window PC 버전)](#r-studio설치window-pc-버전)
	- [R Studio 환경설정](#r-studio-환경설정)
  
## RestAPI란 무엇인가?
---
RestAPI는 Representational State Transfer의 약자이며 클라이언트(웹브라우저, 모바일)가 필요한 자원이 있을때, 서버에게 요청하는 방식을 정의한 API 디자인이다.
기본적으로 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문에 웹의 장점을 최대한 활용할 수 있는 아키텍처 스타일이다.

- HTTP Method인 POST, GET, PUT, DELETE를 통해 해당 자원에 대한 CRUD 매커니즘이 적용가능한 개념이라고 할 수 있다.
- CRUD(Create:생성(POST), Read:조회(GET), Update:수정(PUT), Delete:삭제(DELETE))

## Restful API?   
---
Restful API에 대한것은 명확하게 정의내려진것이 없으며, 일반적으로 REST 구조를 만족시키는 포맷을 Restful 하다고 이야기한다. 

## RestAPI에 대한 예시
---
1. Front-end 개발단에서의 Restapi 활용예시
![그림1](https://zunoxi.github.io/assets/img/dev/web/restapi/vscode.png)
`vue`를 이용해서 웹 프론트엔드단에서 입력한 데이터를 바인딩해서 서버단으로 전송

## R Studio설치(Window PC 버전)   
---
R Studio는 R 프로그램을 보다 편리하게 활용하기 위한 IDE(통합개발환경)으로 GUI를 지원하며 프로젝트 관리 및 패키지 설치 관리, 환경 설정을 용이하게 해주므로 개발 생산성 향상을 위해 반드시 설치하는 것이 좋다. 마찬가지로 R만큼 설치과정이 단순하다.  

1. R Studio 공식사이트에 접속 후, `Download RStudio`버튼을 클릭 : [`https://www.rstudio.com/`](https://www.rstudio.com/)  
![그림8](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-8.png)   
2. Free 버전 `DOWNLOAD` 버튼을 클릭(그 외 버전은 사용제품으로 비용을 지불해야 한다.)  
![그림9](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-9.png)
3. `RStudio 1.2.1355-Windows 7+ (64bit)` 버튼을 클릭(버전은 계속 변경됨)  
![그림10](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-10.png)
4. 다운로드 완료 후, `RStudio-1.2.1335.exe`파일을 더블 클릭하여 설치(디폴트로 `Next`만 누르면 설치됨)  
![그림11](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-11.png)
5. 설치가 완료되면 윈도우 시작버튼을 눌러 하단 검색창에 `rstudio`를 입력 후, 검색된 프로그램을 클릭하여 실행한다.   
![그림18](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-18.png)
7. 정상적으로 설치되었는지 확인하기 위해, `Console`탭에서 아래와 같이 소스코드를 입력  
```r
print("welcome")
```
8. 그림과 같이 입력한 문자열이 그대로 나오면 정상적으로 설치가 완료된 것이다. 
![그림12](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-12.png)

## R Studio 환경설정   
---
R Studio을 보다 편리하게 사용하기 위한 몇가지 Tip을 소개한다.

1. 편집기 `인코딩` 방식 변경
   - 그림과 같이 `Tools> Global Options > Code > Saving > Change버튼 > UTF-8선택` 순서로 클릭)  
   ![그림13](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-13.png)
   - 설정을 변경하면 자동으로 R를 껐다 켤것인지 묻는데 `Yes`를 눌러준다.
   ![그림14](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-14.png)

2. 편집기 코딩 폰트 등 스타일 변경 : `Tools> Global Options > Appearance > 폰트, 사이즈, 테마 등 선택`  
   ![그림17](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-17.png)

3. 화면 레이아웃 변경 : `Tools> Global Options > Pane Layout > 화면 위치별 원하는 레이아웃 선택`  
   ![그림16](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-16.png)

4. 자동줄바꿈 기능 해제 : `Tools> Global Options > Code > Editing > Soft-wrap R source files 체크해제`  
   ![그림15](https://zunoxi.github.io/assets/img/dev/r/2019-05-01-dev-r-rinstall-15.png)  
   ※ 참고로 코드 실행 시 `Ctrl+Enter`키를 누르면 멀티라인 실행이 가능하다.


이로써 R을 사용하기 위한 사전작업이 모두 완료되었다. 다음 포스트에는 간단한 R의 사용방법에 대하여 설명하겠다.

