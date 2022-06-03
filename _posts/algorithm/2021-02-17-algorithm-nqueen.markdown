---
layout: post
title: Algorithm 3. [백준] 9663번 nqueen java(자바) 알고리즘 문제풀이
subtitle: nqueen java(자바) 알고리즘 문제풀이
categories: algorithm
tags: algorithm
comments: true
published: true
header-img: img\algorithm\main.png
---

해당 포스팅은 [링크](https://zunoxi.tistory.com/11?category=816767) 로 이전되었습니다.

<!--
## 개요
> [백준] 9663번 nqueen java(자바) 알고리즘 문제풀이와 소스코드 기록
  
- 목차
	- [`문제이해`](#문제이해)
	- [`소스코드`](#소스코드)
  
## nqueen 알고리즘
---
오늘 포스팅할 알고리즘 문제는 [백준] 9663번 nqueen 이다. 대표적인 재귀함수  응요문제로 유명한 nqueen에 대한 풀이과정을 서술하려 하며 문제는 아래 주소에서 확인할 수 있다.

<br>

**문제 링크**

- [https://www.acmicpc.net/problem/9663](https://www.acmicpc.net/problem/9663)

<br>

---
### **문제이해**

<br>

사실 nqueen 문제는 대표적인 재귀함수 문제로 너무나 유명해기에 문제에 대한 설명이 따로 필요 없을것 같긴한데, 해당 문제를 풀이하기 위해서는 체스에서 `퀸(queen)의 이동범위`를 알필요가 있다. 

<br>

문제에서 서술되어있는것처럼 퀸은 한 턴당 가로와 세로, 대각선 방향으로 제한없이 이동 및 공격할 수 있으며, 이에 양의정수 n을 입력받고 n개의 퀸이 가로nx세로n의 체스판에서 모두 살아있는 경우를 구하는것이 문제다.

<br>

---
### **소스코드**

<br>

우선 nxn의 배열을 만든 후 1번째 행에 1열부터 n열까지 한번씩 퀸을 놓는것으로 시작한다.

```java
for(int k = 0; k<n; k++) {
			arr[0][k]=1;
			dfs(1);
			arr[0][k]=0;
		}

```

<br>

dfs 함수에 들어가게 되면 `checkcnt( ) 함수`를 통해 현재 퀸이 n개 만큼 체스칸에 배치가 되었는지 확인한다. checkcnt()를 만족하지 않는다면 방문배열과 check() 함수를 통해 조건을 만족할경우 퀸을 배치한 후, 다음줄에 배치 가능한지 `재귀로 dfs()를 다시 호출`한다.

```java
public static void dfs(int x) {
		if(checkcnt()) {
			cnt++;
			return;
		}
		for(int i = 0; i<n; i++) {
			if(!visited[x][i] && !(check(x, i))) {
					arr[x][i] = 1;
					visited[x][i] = true;
					dfs(x+1);
					arr[x][i]=0;
					visited[x][i]=false;
			}
		}
	}
```
<br>


check() 함수를 통해 지금 배치하려고 하는칸의 `가로, 세로, 대각선`에 내가 아닌 다른 퀸이 존재 하는지 확인한다. 사실 위에서부터 검사하며 내려오는거라 이보다 더 간단한 방법으로 확인할 수 있을 것 같다. 알고리즘 구현에 더 익숙하고 두뇌회전이(?) 빠른사람은 더 효율적인 코드를 생각해 낼것같다..

```java
public static boolean check(int x, int y) {
		for(int z = 0; z<n; z++) { // 가로 검사 
			if(arr[x][z]==1) {
				return true;
			}
		}
		for(int z = 0; z<n; z++) { // 세로 검사 
			if(arr[z][y]==1) {
				return true;
			}
		}
		for(int z = x, z1 = y; z>-1 && z1>-1; z--,z1--) { // 좌상
			if(arr[z][z1]==1) {
				return true;
			}
		}
		for(int z = x, z1 = y; z<n && z1>-1; z++,z1--) { // 좌하
			if(arr[z][z1]==1) {
				return true;
			}
		}
		for(int z = x, z1 = y; z<n && z1<n; z++,z1++) { // 우하
			if(arr[z][z1]==1) {
				return true;
			}
		}
		for(int z = x, z1 = y; z>-1 && z1<n; z--,z1++) { // 우상
			if(arr[z][z1]==1) {
				return true;
			}
		}
		return false;
	}
```

<br>

dfs가 실행될때마다 퀸의 개수를 확인하는 함수이며 전체 퀸의 개수가 n의 수와 같으면 cnt를 증가시킨다.

```java
	public static boolean checkcnt() {
		int cntq = 0;
		for(int x = 0; x<n; x++) {
			for(int y = 0; y<n; y++) {
				if(arr[x][y]==1) {
					cntq++;
				}
			}
		}
		if(cntq==n) {
			return true;
		}
		return false;
	}

```
<br>

최종 정답 소스 코드

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


<br>

![그림1](/assets/img/algorithm/nqueen/result.png)

시간이...엄청 길게 나온다ㅜㅜ 비효율적인 부분이 많았나보다.

(삼성swexpert nqueen문제에서는 동일한 코드로 170ms 정도 나왔던것 같다.) 

<br>

사실 이문제 처음풀때는 재귀를 막 접했을때라 다른분 블로그를 보고, 1차원 배열을 사용해서 풀었었는데 그것도 좋은 방법인것 같다. 일차원 배열도 한번 고려해보고 다방면으로 문제에 접근하는것이 좋을 것 같다.

-->
