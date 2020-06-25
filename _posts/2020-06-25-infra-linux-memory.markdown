---
layout: post
title: Linux 메모리 사용량 확인 및 관리 
subtitle: (CentOS)Linux 메모리 사용량 확인 및 관리
categories: infra
tags: infra linux
comments: true
published: true
header-img: img/infra/linux/memory/memory.jpg
---

## 개요
---
> Linux 메모리 사용량 확인 및 관리


아래의 블로그들이 내용정리에 큰 도움이 되었다.
[`WhatTap`](https://www.whatap.io/ko/blog/37/)
[`https://brunch.co.kr/@alden/25`](https://brunch.co.kr/@alden/25)


### 리눅스 메모리 사용량 확인
---

리눅스 쉘에 아래와 같은 명령어를 입력한다.

> `#free`

![그림1](https://zunoxi.github.io/assets/img/infra/linux/memory/free.png)


- 용어 설명

    - total : 서버에 할당된 전체 물리적 메모리의 크기

    - used : 실제 사용중인 메모리의 크기 (total에서 free, buff/cache를 뺀 크기) 

    - free : 사용중이 아닌 메모리 크기(total에서 used, buff/cache를 뺀 크기)

    - shared : 공유 메모리 크기

    - buff/cache : 버퍼/캐시 메모리로 사용되고 있는 메모리 크기

    - available : 실질적으로 사용가능한 메모리 크기(`swapping` 없이 할당 가능한 크기)


> [`Swapping?`]

운영체제단에서 메모리 공간에서 용량의 한계 이상으로 프로세스 요청이 들어왔을 시

가장 이벤트가 발생된지 오래된 프로세스를 하드디스크나 SSD같은 Secondary Storage에 저장(`Swap out`)하고

새로 요청된 프로세스를 실행시키는 동작 (램의 보조역할)

다시 기존에 Swap out 시켰던 프로세스를 호출시켰을때 메모리로 불러들이는 것을 `Swap in` 이라고 한다.

> 리눅스 파티션 할당시 swap 파티션을 만들어 이 역할을 하게 할 수 있다. 단, 하드디스크는 메모리 만큼의
속도를 낼 수 없기때문에 궁극적인 램의 대응책이 될 수 없음을 유의한다.


- 자주쓰는 옵션
	- h : 가독성 높게 출력
	- [-b | -k | -m | -g ] : 바이트, 키비바이트, 메비바이트, 기비바이트 단위로 출력한다.
	- w : cache와 buffers를 따로 출력할 수 있게한다.
	- t : 합계를 계산 한것 까지 출력한다.


### buffer? cache?
---

리눅스는 항상 여유 메모리 공간을 `buffer`와 `cache`로 사용하려고 시도한다.

이는 메모리에 데이터를 저장해서 느린 디스크로의 접근을 최대한 줄이려고 하는 것이다.



### page cache?

리눅스는 파일 I/O의 성능 향상을 위해 페이지 캐시라는 메모리 영역을 만들어서 사용한다.

이는 한 번 읽은 파일의 내용을 페이지 캐시라는 영역에 저장해놓았다가

후에 동일한 요청이 들어왔을때, 디스크에 접근하지 않고 페이지 캐시에서 바로 꺼내 쓰는 방식이다.

페이지 캐시는 파일의 내용을 저장하는 캐시이다.



### buffer cache?

`buffer`는 버퍼캐시로 디바이스 블록에 대한 메타데이터들을 메모리에 캐싱한 크기.

* 블록디바이스 : Block 단위로 입출력을 하는 Device (Block은 File System의 섹터를 의미)

페이지 캐시와 비슷한 용도이나 파일시스템의 메타데이터와 관련된 블록을 저장한다는 차이가 있다.



