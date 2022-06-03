---
layout: post
title: Web 4. Java에서 Json 타입으로 RestAPI 활용
subtitle: Java프로젝트에서 RestAPI 활용하여 POST 전송하기
categories: programming
tags: programming web
comments: true
published: true
---

해당 포스팅은 [https://zunoxi.tistory.com/54?category=816528](https://zunoxi.tistory.com/54?category=816528) 로 이전되었습니다.

<!--
## 개요
> Java프로젝트에서 `RestAPI` 활용하여 데이터를 `Json` 형식으로 `POST`
  
- 목차
    - [`Java 프로젝트에서의 RESTapi`](#Java-프로젝트에서의-RESTapi)
    - [`구조`](#구조)
    - [`Sample 예제`](#Sample-예제)
  
## Java 프로젝트에서의 RESTapi
---
[`이전 포스팅`](https://zunoxi.github.io/programming/2020/06/15/dev-web-restapi/)에서는 `RestAPI 방식`을 활용해 Vue 컴포넌트에서 자바스크립트로 코드를 작성하고 서버까지 프론트단에서 받은 데이터가 정상으로 전달 되는지 확인했었다.

<br>

다음으로 확인해 볼 부분은 내가 설계한 백엔드단 로직에서 RestAPI 방식으로  

다른 서버에 POST 전송을 해보는 부분이다.

> 이는 html같은 프론트단이 아닌 `java 프로그래밍 계층`에서 RestAPI를 사용해보는 방법이다. 

[`post 관련 소스`](https://okky.kr/article/235600)가 글을 정리하는데 큰 도움이 되었다.



### 구조
---

> java object => json 변환 => http post 전송 => server 데이터 전송 확인



### Sample 예제
---

- (1) `java 자료형 선언`

		JSONObject json = new JSONObject();     // JSON 변환용 오브젝트 선언
        String custMsgSn = "test";            // 서버에 전송할 데이터 선언
		String senderKeyy = "test";
		String phoneNum = "test";
		String templateCode = "test";
		String message = "test";
		String smsSndNum = "test";
		String smsKind = "test";
		String smsMessage = "test";
		String lmsMessage = "test";
		String subject = "test";
		String button = "test";
    }

- (2) `JSON 변환`
    
    JSON형식으로 변환하기 위해서는 `json-simple-1.1.1.jar` 라는 라이브러리 파일이 필요하다.

    다음 [`링크`](https://repo1.maven.org/maven2/com/googlecode/json-simple/json-simple/1.1.1/) 를 참고하자.

        json.put("id", custMsgSn);
		json.put("keyy", senderKeyy);
		json.put("phone", phoneNum);
		json.put("code", templateCode);
		json.put("mess", message);
		json.put("snd", smsSndNum);
		json.put("kind", smsKind);
		json.put("smes", smsMessage);
		json.put("lmes", lmsMessage);
		json.put("sub", subject);
		json.put("but", button);

- (3) `http post` 전송

		URL postUrl = new URL("http://localhost:9091/api/addalarm"); // RestAPI 서버 및 요청 양식 준수
		HttpURLConnection connection = (HttpURLConnection) postUrl.openConnection();
		connection.setDoOutput(true); 				
		connection.setInstanceFollowRedirects(false);  
		connection.setRequestMethod("POST");  // post 방식으로 전송
		connection.setRequestProperty("Content-Type", "application/json");
		OutputStream os= connection.getOutputStream();
		os.write(body.getBytes());
		os.flush();
		System.out.println("Location: " + connection.getHeaderField("Location"));
		  BufferedReader br = new BufferedReader(new InputStreamReader(
				  (connection.getInputStream())));
				   
				  String output;
				  System.out.println("Output from Server .... \n");
				  while ((output = br.readLine()) != null) {
				  System.out.println(output);
				  }
		connection.disconnect();		

- (4) `server` 구조

> Restapi 서버 구조는 [`링크`](https://zunoxi.github.io/programming/2020/06/15/dev-web-restapi/)를 참고하면 될것같다.


그리고 해당 java 파일을 실행시키고 RestAPI 서버를 확인하면 데이터가 들어오는것을 확인할 수 있다.
