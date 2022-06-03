---
layout: post
title: Algorithm 5. [백준] 17471번 게리맨더링 java 알고리즘 문제풀이
subtitle: 게리맨더링 java(자바) 알고리즘 문제풀이
categories: algorithm
tags: algorithm
comments: true
published: true
header-img: img\algorithm\main.png
---

해당 포스팅은 [링크](https://zunoxi.tistory.com/13?category=816767) 로 이전되었습니다.

<!--
## 개요
> [백준] 17471번 게리맨더링 java 알고리즘 문제풀이
  
- 목차
    - [`문제이해`](#문제이해)
	- [`소스코드`](#소스코드)
  
## 게리멘더링 알고리즘
---
오늘 포스팅할 알고리즘 문제는 [백준] 17471번 '게리맨더링'이다. 19년도 삼성 A레벨 상시 역량테스트에 출제되었던 문제로 유명하다. 물론, 필자는 당시 조합에 익숙하지 않아서 풀지 못했던 문제이다...문제는 아래 주소에서 확인할 수 있다.

<br>

**문제 링크**

- [https://www.acmicpc.net/problem/17471](https://www.acmicpc.net/problem/17471)

<br>

---
### **문제이해**

<br>

이문제는 세 부분으로 나눠서 구현할 수 있을것 같다.

1. 2개의 지역구로 나눌수 있는 부분집합 구하기

2. 지역구가 정상적으로 나눠졌는지 확인하기

3. 정상적인 경우 지역구 별 인구수 차이를 구한 후 최솟값 갱신하기

<br>


---
### **소스코드**

<br>

데이터들을 입력받는다.

```java
// 지역별 인구수 입력받기
        for(int i = 1; i<N+1; i++) {  
            peo[i] = Integer.parseInt(st.nextToken());
        }
        
        // 지역별 연결되어 있는 곳 이차원 배열로 입력받기
        for(int x = 1; x<N+1; x++) { 
            s = br.readLine();
            st = new StringTokenizer(s);
            int each = Integer.parseInt(st.nextToken());
            for(int y = 0; y<each; y++) {
                int spot = Integer.parseInt(st.nextToken());
                link[x][spot] = 1;  // 연결된 지역은 1로 표기
            }
        }

```

<br>

1. 1번 2개의 지역구로 나눌수 있는 부분집합을 구하기

```java
// 0~부터 (N/2) 까지의 부분집합 구하기
        for(int i = 0; i<(N/2)+1; i++) { 
            K = i;
            check = new boolean[N+1];
            pick(1,0);
        }

public static void pick(int start, int cnt) {
        if(cnt == K) {
            sum = Integer.MAX_VALUE;
            temp = new int[N+1];
            // 부분집합으로 선택된 숫자는 temp를 1 
            // 그렇지 않은 숫자는 0, 두 구역으로 분리 한다.
            for(int i = 1; i<N+1; i++) {
                if(check[i] == true) {
                    temp[i] = 1;
                }
                else {
                    temp[i] = 0;
                }
            }
            confirm();
            Min = Math.min(Min, sum);
            return;
        }
        for(int i = start; i<N+1; i++) {
            check[i]=true;
            pick(i+1,cnt+1);
            check[i]=false;
        }
    }

```
<br>


2. 지역구가 정상적으로 나눠졌는지 확인하기

3. 정상적인 경우 지역구 별 인구수 차이를 구한 후 최솟값 갱신하기

```java
public static void confirm() {
        // 모든 지역구가 나눠지는지 확인하는 배열
        visited = new boolean[N+1];
        int people1 = 0;
        int people2 = 0;
        for(int i = 1; i<N+1; i++) {
            // 연결된곳을 gary 메소드를 통해 재귀로 찾는다.
            if(temp[i] == 1 && !visited[i]) {  
                partsum = 0;
                gary(i);
                people1 = partsum;
                break;
            }
        }
        
        for(int i = 1; i<N+1; i++) {
            if(temp[i] == 0 && !visited[i]) {  
                partsum = 0;
                gary(i);
                people2 = partsum;
                break;
            }
        }
        
        // 한곳이라도 방문하지 않은곳이 있다면 턴 종료
        for(int i = 1; i<N+1; i++) {
            if(!visited[i]) {
                return;
            }
        }
        sum = Math.abs(people2-people1);
    }
    
    public static void gary(int x) {
        visited[x] = true;
        partsum = partsum + peo[x];
        for(int i = 1; i<N+1; i++) {
            // 방문했는지, 같은 지역구에 속했는지, 연결이 되어있는 지역인지 차례로 검사
            if(!visited[i] && temp[i] == temp[x] && link[i][x] == 1) {
                gary(i);
            }
        }
    }

```


<br>

전체 소스코드

```java

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
 
public class Main {
    static int N, K, Min, sum, partsum;
    static int [] peo;
    static int [][] link;
    static boolean [] check;
    static int [] temp;
    static boolean [] visited;
 
    public static void main(String[] args) throws NumberFormatException, IOException {
        // TODO Auto-generated method stub
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        peo = new int [N+1];
        link = new int [N+1][N+1];
        
        String s = br.readLine();
        StringTokenizer st = new StringTokenizer(s);
 
        for(int i = 1; i<N+1; i++) {  
            peo[i] = Integer.parseInt(st.nextToken());
        }
        
        for(int x = 1; x<N+1; x++) { 
            s = br.readLine();
            st = new StringTokenizer(s);
            int each = Integer.parseInt(st.nextToken());
            for(int y = 0; y<each; y++) {
                int spot = Integer.parseInt(st.nextToken());
                link[x][spot] = 1;  
            }
        }
                
        Min = Integer.MAX_VALUE;
        
        for(int i = 0; i<(N/2)+1; i++) { 
            K = i;
            check = new boolean[N+1];
            pick(1,0);
        }
        
        if(Min == Integer.MAX_VALUE) {  
            System.out.println(-1);
        }
        else {
            System.out.println(Min);
        }
    }
    
    public static void pick(int start, int cnt) {
        if(cnt == K) {
            sum = Integer.MAX_VALUE;
            temp = new int[N+1];
            for(int i = 1; i<N+1; i++) {
                if(check[i] == true) {
                    temp[i] = 1;
                }
                else {
                    temp[i] = 0;
                }
            }
            confirm();
            Min = Math.min(Min, sum);
            return;
        }
        for(int i = start; i<N+1; i++) {
            check[i]=true;
            pick(i+1,cnt+1);
            check[i]=false;
        }
    }
    
    public static void confirm() {
        visited = new boolean[N+1];
        int people1 = 0;
        int people2 = 0;
        for(int i = 1; i<N+1; i++) {
            if(temp[i] == 1 && !visited[i]) {  
                partsum = 0;
                gary(i);
                people1 = partsum;
                break;
            }
        }
        
        for(int i = 1; i<N+1; i++) {
            if(temp[i] == 0 && !visited[i]) {  
                partsum = 0;
                gary(i);
                people2 = partsum;
                break;
            }
        }
        
        for(int i = 1; i<N+1; i++) {
            if(!visited[i]) {
                return;
            }
        }
        sum = Math.abs(people2-people1);
    }
    
    public static void gary(int x) {
        visited[x] = true;
        partsum = partsum + peo[x];
        for(int i = 1; i<N+1; i++) {
            if(!visited[i] && temp[i] == temp[x] && link[i][x] == 1) {
                gary(i);
            }
        }
    }
 
}

```
-->
