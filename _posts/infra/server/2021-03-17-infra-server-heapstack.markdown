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

## 개요
> 프로그램이 운영체제로부터 할당받는 Heap, Stack 메모리 영역에대한 정리
  
- 목차
	- [`Log Handling`](#Log-Handling)
	- [`tomcat 로그 종류`](#tomcat-로그-종류)
  
### Heap, Stack
---
얼마전 Memory Leak이 의심되는 WAS의 Heap Dump를 떠서 살펴볼일이 있었다. 업무를하면서 Heap Dump에 대한 이야기를 많이 들었는데, Heap dump가 뭔지도 잘 모를뿐더러, 문득 학부때 배웠던 메모리 Heap과 Stack 영역에 대한 기억이 잘 나지 않았다.. 이김에 본 포스팅을 작성하며 개념을 명확히 잡으려한다.

<br><br>



#### 메모리 구조

---

일반적으로 프로그램이 운영체제로부터 
