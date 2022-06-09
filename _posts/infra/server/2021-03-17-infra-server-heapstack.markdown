---
layout: post
title: Server 5. 메모리구조(heap, stack) 이해
subtitle: 메모리구조(heap, stack) 이해
categories: infra
tags: infra server
comments: true
published: true
header-img: img/infra/linux/memory/memory.jpg
---

해당 포스팅은 [https://zunoxi.tistory.com/100?category=950184](https://zunoxi.tistory.com/100?category=950184)으로 이전되었습니다.

<!--

## 개요
> 프로그램이 운영체제로부터 할당받는 Heap, Stack 메모리 영역에대한 정리
  
- 목차
	- [`메모리 구조`](#메모리-구조)
  
### Heap, Stack
---
얼마전 Memory Leak이 의심되는 WAS의 Heap Dump를 떠서 살펴볼일이 있었다. 업무를하면서 Heap Dump에 대한 이야기를 많이 들었는데, Heap dump가 뭔지도 잘 모를뿐더러, 문득 학부때 배웠던 메모리 Heap과 Stack 영역에 대한 기억이 잘 나지 않았다.. 이김에 본 포스팅을 작성하며 개념을 명확히 잡으려한다.

<br>

**참고했던 블로그 포스트**

-   [http://www.tcpschool.com/c/c_memory_structure](http://www.tcpschool.com/c/c_memory_structure)
-   [https://jihyeong-ji99hy99.tistory.com/20](https://jihyeong-ji99hy99.tistory.com/20)

<br><br>


#### 메모리 구조

---

일반적으로 프로그램이 운영체제로부터 `Code, Data, Stack, Heap` 의 메모리 영역을 할당 받는다. 그 영역별 특징은 다음과 같다.

<br>

**`코드(Code)`** 

>  메모리의 코드영역은 실행할 프로그램의 코드가 저장되는 일종의 테스트영역. 

  - 일반적으로 CPU는 코드 영역에 저장된 명령어를 하나씩 가져가서 처리한다.
  - 상수를 저장하고 프로그램의 시작부터 끝까지 메모리에 남아있다.


<br>

**`데이터(Data)`** 

> 메모리의 데이터영역은 프로그램의 전역 변수와 정적변수가 저장되는 영역 

프로그램이 시작부터 끝까지 메모리에 남아있다.(전역변수가 프로그램의 시작부터 끝까지 존재하는 이유)

<br>

**`힙(Heap)`**

> 사용자에 의해 메모리 공간이 동적으로 할당과 해제됨

  - 메모리의 낮은 주소애서 높은 주소로 할당됨
  - LILO(Last In Last Out)방식 : 가장 늦게 저장된 데이터가 가장 늦에 인출
  - C에서는 malloc(), Java에서는 new 연산자로 할당
  - Java에서 가비지 컬렉터가 이를 일정수준 이상일때 해제시킨다.
    - `가비지 컬랙터(GC)` : C언어의 경우 별도의 메모리를 해제시키는 문법이 있지만 JAVA는 없다. OS로부터 메모리를 할당받고 반납하는 전 과정에 가비지 컬렉터가 개입되며 ‘주소를 잃어버린 메모리’(가비지)를 정리해주는 역할을 한다.  JVM이 OS에 추가로 메모리를 요청할 때와 idle time에 가비지 컬렉터가 실행된다.

<br>

**`스택(Stack)`** 

> 함수의 호출과 관계된 지역변수와 매개변수가 저장되는 영역

  - 메모리의 높은 주소에서 낮은 주소로 할당됨
  - LIFO(Last In First Out)방식 : 가장 늦게 저장된 데이터가 가장 먼저 인출
  - 프로그램이 자동으로 사용하는 임시 메모리 영역
  - 컴파일 시 크기가 결정됨


<br>

---
> 참고

- Stack Overflow : 스택이 힙 영역을 침범하는 경우
- Hip Overflow : 힙이 스택 영역을 침범하는 경우

-->
