---
layout: post
title: Algorithm 2. [백준] 17070번 파이프옮기기 java 알고리즘 문제풀이
subtitle: 파이프옮기기 java 알고리즘 문제풀이
categories: algorithm
tags: algorithm
comments: true
published: true
header-img: img\algorithm\main.png
---

해당 포스팅은 [링크](https://zunoxi.tistory.com/9?category=816767) 로 이전되었습니다.

<!--
## 개요
> [백준] 17070번 파이프옮기기 java 알고리즘 문제풀이와 소스코드 기록
  
- 목차
	- [`문제이해`](#문제이해)
	- [`소스코드`](#소스코드)
  
## 파이프옮기기 알고리즘
---
오늘 포스팅할 알고리즘 문제는 \[백준\] 17070번 파이프옮기기이다. 최근 재귀함수 구현에 대해 공부하고 있었는데 마침 관련 문제를 발견해서 풀이과정을 기록한다. 문제는 아래 주소에서 확인할 수 있다.

<br>

**문제 링크**

- [https://www.acmicpc.net/problem/17070](https://www.acmicpc.net/problem/17070)

<br>

---
### **문제이해**

<br>

먼저 문제를 정독 후 `재귀함수`를 이용해서 풀어봤다. 일반적인 길찾기 문제처럼 풀면 되지만, boolean 배열로 지나간 곳인지 체크해줄 필요가 없다는것이 인상적였다.

`파이프가 가로, 세로, 대각선인 경우들의 알고리즘`만 잘구현한다면 큰 문제 없었던 문제인것 같다. (물론, 필자는 코드를 허술하게 짜서 시간이 꽤 걸렸다..ㅠㅠ)

<br>

우선 필자는 테두리를 0으로 만들어 준 후 입력값이 0인 경우를 -> 2 로 만들어 줬다. 즉, 빈 공간이 2인 상태이다. (모두 2로 만들고 0값을 그냥 받아도 문제 없다.) 다음 2개의 배열 공간을 차지할 파이프는 5로 표시했다. 아래는 위 문제를 구현한 소스 코드이다. 

<br>

---
### **소스코드**

<br>

dfs라는 함수는 파이프의 오른쪽칸을 기준으로 시작한다.

```java
arr = new int[N+2][N+2];
        for(int x = 1; x<N+1; x++) {
            String s = br.readLine();
            StringTokenizer st = new StringTokenizer(s);
            for(int y = 1; y<N+1; y++) {
                int c = Integer.parseInt(st.nextToken());
                if(c==0) {
                    arr[x][y]=2;
                }
                else {
                    arr[x][y]=c;
                }
            }
        }
        arr[1][1]=5; arr[1][2]=5; // 처음 파이프는 가로로 누워있다. 
        
        cnt = 0;
        int x = 1; int y = 2; // 가로의 오른쪽칸에서 출발
        
        dfs(x,y);


```

<br>

먼저, 파이프가 가로인 경우이다.

```java
if(arr[x][y-1]==5) { // 가로일때
                    if(arr[x][y+1]==2 ) { // 가로확인
                        arr[x][y-1]=2; arr[x][y+1]=5;
                        dfs(x,y+1);
                        arr[x][y-1]=5; arr[x][y+1]=2;
                    }
                    if(arr[x+1][y+1]==2 && arr[x][y+1]!=1 && arr[x+1][y]!=1) { // 대각선 확인
                        arr[x][y-1]=2; arr[x+1][y+1]=5;
                        dfs(x+1,y+1);
                        arr[x][y-1]=5; arr[x+1][y+1]=2;
                    }
                }
```
<br>

세로인 경우

```java
if(arr[x-1][y]==5) { // 세로일때
                    if(arr[x+1][y]==2) { // 세로확인
                        arr[x-1][y]=2; arr[x+1][y]=5;
                        dfs(x+1,y);
                        arr[x-1][y]=5; arr[x+1][y]=2;
                    }
                    if(arr[x+1][y+1]==2 && arr[x][y+1]!=1 && arr[x+1][y]!=1) { // 대각선 확인
                        arr[x-1][y]=2; arr[x+1][y+1]=5;
                        dfs(x+1,y+1);
                        arr[x-1][y]=5; arr[x+1][y+1]=2;
                    }
                }
```

<br>

마지막으로 대각선으로 배치되어 있을 경우

```java
if(arr[x-1][y-1]==5) {
                    if(arr[x][y+1]==2) { // 가로확인
                        arr[x-1][y-1]=2; arr[x][y+1]=5;
                        dfs(x,y+1);
                        arr[x-1][y-1]=5; arr[x][y+1]=2;
                    }
                    if(arr[x+1][y]==2) { // 세로확인
                        arr[x-1][y-1]=2; arr[x+1][y]=5;
                        dfs(x+1,y);
                        arr[x-1][y-1]=5; arr[x+1][y]=2;
                    }
                    if(arr[x+1][y+1]==2 && arr[x][y+1]!=1 && arr[x+1][y]!=1) { // 대각선 확인
                        arr[x-1][y-1]=2; arr[x+1][y+1]=5;
                        dfs(x+1,y+1);
                        arr[x-1][y-1]=5; arr[x+1][y+1]=2;
                    }
                }

```


이를 이용해서 구현하면 된다. 주의 할만한점은 대각선으로 파이프가 위치할때는 양 옆의 빈공간에도 벽이 존재하면 안된다는 점이다.

<br>

아래는 전체 소스 코드이다.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;

public class Main {
    static int cnt, N ;
    static int[][] arr;
    static boolean [][] visited;

    public static void main(String[] args) throws NumberFormatException, IOException {
        // TODO Auto-generated method stub
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        arr = new int[N+2][N+2];
        for(int x = 1; x<N+1; x++) {
            String s = br.readLine();
            StringTokenizer st = new StringTokenizer(s);
            for(int y = 1; y<N+1; y++) {
                int c = Integer.parseInt(st.nextToken());
                if(c==0) {
                    arr[x][y]=2;
                }
                else {
                    arr[x][y]=c;
                }
            }
        }
        arr[1][1]=5; arr[1][2]=5; // 처음 파이프는 가로로 누워있다. 
        
        cnt = 0;
        int x = 1; int y = 2; // 가로의 오른쪽칸에서 출발
        
        dfs(x,y);
        
        System.out.println(cnt);
        
        }
        
        public static void dfs(int x,int y) {
            if(x==N && y==N) {
                cnt++;
                return;
            }
            else {
                if(arr[x][y-1]==5) { // 가로일때
                    if(arr[x][y+1]==2 ) { // 가로확인
                        arr[x][y-1]=2; arr[x][y+1]=5;
                        dfs(x,y+1);
                        arr[x][y-1]=5; arr[x][y+1]=2;
                    }
                    if(arr[x+1][y+1]==2 && arr[x][y+1]!=1 && arr[x+1][y]!=1) { // 대각선 확인
                        arr[x][y-1]=2; arr[x+1][y+1]=5;
                        dfs(x+1,y+1);
                        arr[x][y-1]=5; arr[x+1][y+1]=2;
                    }
                }
                else if(arr[x-1][y]==5) { // 세로일때
                    if(arr[x+1][y]==2) { // 세로확인
                        arr[x-1][y]=2; arr[x+1][y]=5;
                        dfs(x+1,y);
                        arr[x-1][y]=5; arr[x+1][y]=2;
                    }
                    if(arr[x+1][y+1]==2 && arr[x][y+1]!=1 && arr[x+1][y]!=1) { // 대각선 확인
                        arr[x-1][y]=2; arr[x+1][y+1]=5;
                        dfs(x+1,y+1);
                        arr[x-1][y]=5; arr[x+1][y+1]=2;
                    }
                }
                else if(arr[x-1][y-1]==5) {
                    if(arr[x][y+1]==2) { // 가로확인
                        arr[x-1][y-1]=2; arr[x][y+1]=5;
                        dfs(x,y+1);
                        arr[x-1][y-1]=5; arr[x][y+1]=2;
                    }
                    if(arr[x+1][y]==2) { // 세로확인
                        arr[x-1][y-1]=2; arr[x+1][y]=5;
                        dfs(x+1,y);
                        arr[x-1][y-1]=5; arr[x+1][y]=2;
                    }
                    if(arr[x+1][y+1]==2 && arr[x][y+1]!=1 && arr[x+1][y]!=1) { // 대각선 확인
                        arr[x-1][y-1]=2; arr[x+1][y+1]=5;
                        dfs(x+1,y+1);
                        arr[x-1][y-1]=5; arr[x+1][y+1]=2;
                    }
                }
            }
        }
    }
```
-->
