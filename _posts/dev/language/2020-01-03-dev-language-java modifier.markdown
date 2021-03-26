---
layout: post
title: Language#3. JAVA 개념정리 (접근제한자)
subtitle: JAVA 개념정리 세번째 접근제한자란
categories: programming
tags: programming language
comments: true
published: true
header-img: img/dev/language/java/java.png
---

자바 개념정리 마지막 3번째 포스팅이며, 이번 포스팅에서는 접근제한자에 대한 글을 기술한다.


<br>


## Modifier 

---

<br>


클래스 정의시에 접근, 사용에 대한 제한 주기 위해 제한자(Modifier)를 사용할 수 있다. 


<br>


> static

<br>

- static 제한자는 member variables, member methods, and member nested classes 앞에 사용한다.

- static variable : 멤버변수 정의시에 static 키워드를 선언하여 정의한다. static으로 정의된 변수는 객체 생성 없이 사용할 수 있다. 그 클래스를 instance한 객체들 간의 공용변수로 사용한다.

- static method : 메서드 정의부분에 static 키워드를 사용하여 정의한다. (객체 생성 없이 사용가능) 

- static 메서드는 객체 생성 없이 사용되므로 메서드 안에서 static 변수만 사용할 수 있다. 일반 변수나 메서드는 사용 불가/ 일반변수나 메서드는 사용 불가/ this, super 변수 사용 불가 / overriding  안된다.

- static block : 클래스가 메모리에 로딩된 후 자동 호출된다. 


<br>


> final

<br>

- 마지막을 의마하며 클래스 앞에 정의하여 더 이상 상속 받을 수 없음을, 메서드 앞에 정의하여 Overriding 할 수 없음을, 변수 앞에 정의하여 값 변경할 수 없음을 즉, 상수임을 의미한다.

- interface는 변수를 사용할 수 없고, 모두 상수로 정의된다.



* abstract

- 일부 구현되지 않은 메서드를 포함할 수 있는 클래스 정의 시에 사용하며, 구현되지 않은 메서드 정의 시에 사용한다.
​

<br>


# Interface


<br>

인터페이스는 상수와 구현되지 않은 메서드로만 구성된다. 상수 정의시에 특별히 상수라고 명시하지 않아도 컴파일러에 의해서 상수로 변경된다. 또한 메서드 정의시에 public 하게 정의하지 않아도 인터페이스의 모든 메서드는 컴파일러가 public 제한자를 삽입한다.

<br>


- 인터페이스는 객체 생성을 할 수 없다.

- 구현되지 않은 메서드를 포함 하기 때문이다.

- super type으로 사용할 수 있다.

- 상속되어져서 sub class가 모든 메서드 구현(Overriding)해야 한다.

- 상속한 sub class들의 명세서 역할로 사용

<br>

> Collection API

<br>

- Collection API는 객체들을 저장, 검색, 삭제하는 기능 등을 제공하는 클래스들의 집합이다.

- java.util.package에 정의되어 있다.

- Collection :  모든 클래스들의 Object를 요소(element)로 저장하는 객체의 최상위 Interface


<br>

> Set

<br>

- 객체 저장 시 순서 없이 저장하고, 같은 데이터(equals(), hashCode() 비교시)는 무시된다. 

- HashSet : Set을 구현한 클래스 / 같은 내용의 객체를 equals()와 hashCode()가 같을 경우 같다고 인정하여 추가되는 데이터는 무시한다.

- TreeSet : SortedSet을 구현한 클래스 / sort하여 객체를 관리한다. 



<br>

> List

<br>

- 객체 저장 시에 인덱스를 가지고 순서대로 저장하며, 중복된 데이터도 새로운 인덱스를 부여하여 저장한다.

- 객체 저장 시 index를 가지고 관리(순서대로)

- 중복된 객체도 상관없이 저장된다.





<br>

> Map

<br>

- 데이터 저장, 관리 시에 검색을 보다 쉽게 하기 위해 구현된 클래스이다.

- 객체를 key와 value으로 구분하여 관리한다.

- key를 통한 검색과 삭제를 쉽게한다.





<br>

> java.util - Calander

<br>

- 시간을 기억하는 class이다. / System.currentTimeMillis():long - 현재 시간을 1/1000초로 long 타입으로 리턴한다.



<br>
<br>

# Exception

Exception Overview : Java에서 오류는 객체이고, 크게 두 가지로 분리된다. 프로그램 처리 도중 기대되지 않는 상황(예외)를 Exception 이라 하고, 치명적인 오류를 Error로 클래스로 분리한다.



<br>

> Exception class

<br>

- 정확한 프로그램

- 예외가 발생할지 모르는 상황을 체크해 준다.(Compiler)

- 예외가 발생하더라도 프로그램을 중단시키지 않고, 복구하여 프로그램을 지속적으로 실행 할 수 있도록 한다.

- file not found, DB connection failure 등



<br>

> Error class

<br>

- Fatal situations(serious error)

- Unchecked exceptions

- Not expected to attempt recovery

- 치명적 오류는 SW적으로 복구 불가능

- Compiler가 체크하지 않는다.



<br>

> RunTimeException : 코드내의 처리가 잘못되었을때 발생한다.

<br>

- ArithmeticException : 0으로 나누기를 실행했을 때

- NullPointException : 객체를 생성하지 않고 사용했을 때

- NegativeArraySizeException : 배열의 크기를 음수 값을 주었을 때

- ArrayIndexOutOfBoundsException : 배열의 크기를 벗어났을 때

- SecurityException : 보안에 위배되는 기능을 수행할 때