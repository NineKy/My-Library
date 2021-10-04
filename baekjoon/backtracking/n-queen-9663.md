---
title: '9663'
excerpt: N-Queen
categories:
  - 백준
description: 조금 더 복잡한 백트래킹 문제 1
---

# N-Queen\(9663\)

## 문제

N-Queen 문제는 크기가 N × N인 체스판 위에 퀸 N개를 서로 공격할 수 없게 놓는 문제이다.

N이 주어졌을 때, 퀸을 놓는 방법의 수를 구하는 프로그램을 작성하시오.

## 입력

첫째 줄에 N이 주어진다. \(1 ≤ N &lt; 15\)

## 출력

첫째 줄에 퀸 N개를 서로 공격할 수 없게 놓는 경우의 수를 출력한다.

이게 무슨소리일까

일단 0,0으로 시작해서 한 판을 다 돌면서 확인해본 결과, dfs과정을 거치고 나면 한판의 답은 잘 나온다

그러면 이게 모든 점에서 시작해서 다 돌면서 모든 점에서의 경우의 수를 구하라? 그건가?

## 예제 입력

```text
8
```

## 예제 출력

```text
92
```

첫 번째 풀이는 dfs와 검사 알고리즘을 같이 한꺼번에 구현할려고 시도했었다. 하지만 그냥 검사알고리즘을 따로하고, 그 따로 알고리즘을 dfs에 넣어서 활용하는 방법을 해보라는 조언에 변경시도

## 풀이

```java
import java.io.*;
import java.util.*;

public class Main{
    static boolean visited[][];
    static int count, N;

    public static void main(String[] args) throws IOException{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        N = Integer.parseInt(br.readLine());
        count=0;

        //자바에서 boolean 배열을 생성하는 경우에 false값을 초기화되어있다!!

        visited = new boolean[N][N];
        dfs(visited, 0);
        System.out.println(count);
    }
    public static void dfs(boolean [][]visited, int depth){
        //이전까지의 n&m에서는 여기서 출력을 진행해줬지만 이번에는 숫자를 세는 게 포인트이기때문에 연산만.
        if(depth==N){
            count++;
            return;
        }

        //기본적인 재귀를 사용하는 방식이고,
        for (int i = 0; i < N; i++) {
            if(fQueen(visited, depth, i)){
                visited[depth][i] = true;
                dfs(visited, depth+1);
                visited[depth][i] = false;
            }
        }
    }

    public static boolean fQueen(boolean[][] visited, int x, int y){
        //현재 점에서부터 세로를 검사해주고,
        for (int i = 0; i < x; i++) {
            if(visited[i][y] == true)
                return false;
        }
        //와! for문 안에 변수를 2개나 넣어서 할 수도 있다는 사실!!!!
        //원래는 이중for문써서 해볼려고하니까 그렇게 하기는 시간초과일꺼같기도하고 애초에 틀리기도해서 
        //이부분은 검색해봤더니 for문 안에 변수를 2개를 넣어서 해본사람이 많았다!!
        //위로 대각선 검사해주고,
        for (int i=x, j=y; i>=0&&j>=0; i--, j--) {
            if(visited[i][j] == true)
                return false;
        }
        //아래로 대각선 검사해주고,
        for (int i=x, j=y; i>=0&&j<N; i--, j++) {
            if(visited[i][j] == true)
                return false;
        }

        return true;
    }
}
```

[백준](https://www.acmicpc.net/problem/9663)



## 2번째 시도

```java
import java.util.*;
import java.io.*;
public class Main{
    static int n, sum=0;
    static int[][] arr;
    static boolean[][] visited;
    public static void main(String[] args) throws Exception{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        n = Integer.parseInt(br.readLine());
        
        //퀸의 위치를 기억하기 위한 배열
        arr = new int[n][n];
        //퀸이 지나갈 수 있는 곳을 기억하기 위한 배열
        visited = new boolean[n][n];
        
        //깊이우선탐색
        dfs(0);
        System.out.println(sum);
    }
    
    public static void dfn(int cnt){
        //만약 찾고있는 깊이가 한계까지 도달한다면 결과값 ++
        if(cnt == n){
            sum++;
            return;
        }
        
        for(int i=0; i<n; i++){
            //여기서부터는 기존 dfs의 구현과 유사하다
            
            //일단 만약 체크를 했던 장소라면 넘어가고
            if(visited[cnt][i])
                continue;
            
            //해당 위치에 들렀다고 체크하고
            visited[cnt][i] = true;
            //체크를 했으니까 퀸의 위치또한 저장해두고 진행
            arr[cnt][i] = 1;
            //퀸이 움직일 수 있는 곳을 모두 체크
            QueenMove(cnt, i);
            
            //재귀를 통해서 다음 dfs로 진행
            dfs(cnt+1);
            //만약 깊이가 끝까지 진행되었다면, return을 했을테지만 그냥 진행이 되었다면 여기까지 올 수 있음
            //여기까지 도달했다는 의미는 퀸의 배치를 완료했다는 의미이다.
            //다시 원상복구를 해줘야한다.
            
            //체스판을 초기화 시키고
            for(int j=0; j<n; j++){
                for(int k=0; k<n; k++){
                    visited[j][k] = false;
                }
            }
            //퀸을 빼주고
            arr[cnt][i] = 0;
            
            //이전에 두었던 퀸의 위치를 복원시켜준다
            for(int j=0; j<n; j++){
                for(int k=0; k<n; k++){
                    if(arr[j][k] == 1){
                        QueenMove(j, k);
                    }
                }
            }
            
        }
    }
    
    //좌표를 생각하는게 반대이다..!
    public static void QueenMove(int x, int y){
        //세로로 움직이면서 확인
        for(int i=x; i<n; i++){
            if(!visited[i][y])
                visited[i][y] = true;
        }
        
        //왼쪽 아래 대각선으로 확인
        for(int i=x, j=y; i<n&&j<n; i++, j++){
            if(!visited[i][j])
                visited[i][j] = true;
        }
        
        //오른쪽 아래 대각선으로 확인
        for(int i=x, j=y; i<n&&j>=0; i++, j--){
            if(!visited[i][j])
                visited[i][j] = true;
        }
    }
}

```

dfs의 구조를 조금씩은 알게되어가고 있어서 다행이면 다행이다..!

하지만 어려워서 쉽게 해결하지 못했던 문제이다...





