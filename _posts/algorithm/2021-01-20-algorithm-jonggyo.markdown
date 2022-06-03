---
layout: post
title: Algorithm 1. [정올] 1863번 종교 java 알고리즘 문제풀이
subtitle: 종교 java(자바) 알고리즘 문제풀이
categories: algorithm
tags: algorithm
comments: true
published: true
header-img: img\algorithm\main.png
---

해당 포스팅은 https://zunoxi.tistory.com/8?category=816767 로 이전되었습니다.

\\<!--
## 개요
> [정올] 1863번 종교 java(자바) 알고리즘 문제의 풀이와 소스코드 기록
  
- 목차
	- [`문제이해`](#문제이해)
	- [`소스코드`](#소스코드)
  
## 종교 알고리즘
---
오늘 포스팅할 알고리즘 문제는 정보올림피아드(이하 정올)/1863번 종교문제이다. 최근에 서로소 집합들 관련 공부를 하고 있었는데, 이를 응용해서 풀기 적합 한 문제인 것 같다.


<br>

**문제 링크**

- [http://www.jungol.co.kr/bbs/board.php?bo\_table=pbank&wr\_id=1136&sca=30&sfl=wr\_hit&stx=1863&sop=and](http://www.jungol.co.kr/bbs/board.php?bo_table=pbank&wr_id=1136&sca=30&sfl=wr_hit&stx=1863&sop=and)

<br>

---
### **문제이해**

<br>

이 문제는 일반적인 **`서로소 집합(상호배타집합)`** 을 이용한 문제들처럼` find메소드와 union메소드를 구현` 후 입력받은 쌍 데이터를 이용하여 실시간으로 root값을 갱신시켜주는 방법으로 풀어봤다. 최종적으로는 parents배열에서의 값 중 자기 자신이 root인 것들을 카운트해주는 방식으로 무리의 수를 계산한다.

단, 단순히 find와 union만을 이용해서 문제를 풀면 `시간 초과가 발생`한다. (테스트 케이스를 얼마나 정교하고 복잡하게 짰는지 모르겠지만... 12번 테스트 케이스에서 계속 터졌다.)

그래서 union 과정에서 **`rank`** 라는 깊이 측정용 배열을 사용해 봤다. 트리구조를 이룰 때 본인보다 긴 트리에 가져다 붙임으로  트리의 깊이를 최소화하는 작업을 수행했다.

아래는 위 문제를 구현한 소스 코드이다. 

<br>

---
### **소스코드**

<br>

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.StringTokenizer;
 
public class Main {
    static int [] parents;
    static int [] rank;
 
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String str = br.readLine();
        StringTokenizer st = new StringTokenizer(str);
        int n = Integer.parseInt(st.nextToken()); // 사람 수
        int m = Integer.parseInt(st.nextToken()); // 쌍의 수
        
        parents = new int[n+1];
        rank = new int[n+1];   // 트리의 깊이를 측정
        
        for(int i = 1; i<=n; i++) {
            parents[i] = i;
        }
        
        for(int i = 0; i<m; i++) {
            str = br.readLine();
            st = new StringTokenizer(str);
            int a = Integer.parseInt(st.nextToken()); 
            int b = Integer.parseInt(st.nextToken()); 
            union(a,b);
        }
        
        int cnt = 0;
        for(int i = 1; i<=n; i++) {
            if(i == parents[i]) {
                cnt++;
            }
        }
        System.out.println(cnt);
    }
    
    public static int find(int x) {
        if(x == parents[x]) {
            return x;
        }
        parents[x] = find(parents[x]);
        return parents[x];
    }
    
    public static void union(int x, int y) {
        int px = find(x);
        int py = find(y);
//        parents[px] = py;
        if(rank[px]<rank[py]) {  // 트리의 깊이를 서로 비교해서 작은것을 큰것아래에 붙인다.(트리의 깊이 최소화)
            parents[px] = py;  
        }
        else {
            parents[py] = px;
            if(rank[px] == rank[py]) {
                rank[px]++;
            }
        }
    }
 
}

```
-->
