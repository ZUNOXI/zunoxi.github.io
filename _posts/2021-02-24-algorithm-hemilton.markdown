---
layout: post
title: Algorithm 4. [정올] 1681번 해밀턴 순환회로 java 알고리즘 문제풀이
subtitle: 해밀턴 순환회로 java 알고리즘 문제풀이
categories: algorithm
tags: algorithm
comments: true
published: true
header-img: img\algorithm\main.png
---

## 개요
> [정올] 1681번 해밀턴 순환회로 java 알고리즘 문제풀이와 소스코드 기록
  
- 목차
	- [`소스코드`](#소스코드)
  
## 해밀턴 순환회로 알고리즘
---
오늘 풀어본 문제는 정보올림피아드(이하 정올)/1681번 해밀턴 순환회로 문제이다. 백트래킹만 잘해 준다면 행렬 계산 이용해서 풀수 있는 문제인것 같다. 일단 마구잡이로 풀었던것 같은데 시간 터지는 오류는 안뜨는것 같다.

<br>

**문제 링크**

- [http://www.jungol.co.kr/bbs/board.php?bo_table=pbank&wr_id=954&sca=3030](http://www.jungol.co.kr/bbs/board.php?bo_table=pbank&wr_id=954&sca=3030)

<br>

---
### **소스코드**

<br>

먼저, 태현이는 회사에서부터 출발하니깐 입력값을 그대로 받았다면 X=0일때만 고려해서 dfs 해주면 될것같다.

```
for(int j = 1; j<N; j++) { // 검사는 0컴마 1부터 0컴마 N-1까지 해준다!(회사에서부터 출발하니깐)
            visited = new boolean[N];
            sum = 0;
            if(arr[0][j]!=0) {
                dfs(0,j,1);
            }
        }

```

<br>

다음, 백트래킹을 적절히 활용하여 회사로 가는경우를 구하면 된다.

 

이때, Accepted(60)인 오류같은 경우에는 마지막 배송지에서 회사로 가는방법이 없는 경우의 수 인데, 즉 0 인데 더해주며 최소값이 정답보다 낮게 출력되며 오류가 발생하는것 같다.

 

이 부분만 조금 주의하면 어렵지 않을것이다.

```
public static void dfs(int x, int y, int cnt) {
        sum = sum + arr[x][y];  // 일단 들어오면 sum 값에 더한다.
        visited[x] = true; // 방문한것으로 기억
        if(cnt==(N-1)) {
            sum = sum + arr[y][0];   // 가장 마지막 회사로 다시 가는경우를 더하자
            if(arr[y][0]==0) {  // 이때 회사로 돌아가는 값을 알수 없다면 이길은 못간다!
                return;
            }
            min = Math.min(min, sum);
            sum = sum - arr[y][0];  // 최소값만 구하고 다음 계산을 위해 회사로 가는길은 다시 빼주자.
            return;
        }
        
        
        for(int i = 0; i<N; i++) {
            if(!visited[i] && arr[y][i]!=0) {  // 배송장소가 방문하지 않았고 갈수 있는방법이 있는경우 dfs 탐색
                dfs(y,i,cnt+1);
                sum = sum - arr[y][i]; // 탐색이 종료 되었을때는 다시 값을 빼주고 다음 경우의수 준비
                visited[i] = false; // 방문하지 않은것으로 변경(백트래킹)
            }
        }
    }
```
<br>



> 최종 제출 소스


+ sum 값이 min 값보다 높아지면 빠져나옴으로서 시간줄이기 가능하다.

```
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;
 
public class 해밀턴순환회로 {
    static int[][]arr;
    static boolean[]visited;
    static int min, sum, N;
    
    public static void main(String[] args) throws NumberFormatException, IOException {
        // TODO Auto-generated method stub
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        arr = new int[N][N];
        
        for(int x = 0; x<N; x++) {
            String s = br.readLine();
            StringTokenizer st = new StringTokenizer(s);
            for(int y = 0; y<N; y++) {
                arr[x][y]=Integer.parseInt(st.nextToken());
            }
        }
        min = 123415098;
        for(int j = 1; j<N; j++) { 
            visited = new boolean[N];
            sum = 0;
            if(arr[0][j]!=0) {
                dfs(0,j,1);
            }
        }
        System.out.println(min);
    }
    public static void dfs(int x, int y, int cnt) {
        if(sum>min) {
            return;
        }
        sum = sum + arr[x][y];  
        visited[x] = true; 
        if(cnt==(N-1)) {
            sum = sum + arr[y][0];   
            if(arr[y][0]==0) {  
                return;
            }
            min = Math.min(min, sum);
            sum = sum - arr[y][0];  
            return;
        }
        
        
        for(int i = 0; i<N; i++) {
            if(!visited[i] && arr[y][i]!=0 &&(sum<min)) {  
                dfs(y,i,cnt+1);
                sum = sum - arr[y][i]; 
                visited[i] = false; 
            }
        }
    }
}
```
