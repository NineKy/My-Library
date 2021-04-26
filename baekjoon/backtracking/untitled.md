---
title: '15649'
excerpt: N과 M(1)
categories:
  - 백준
description: 백트래킹 입문 문제 1
---

# N과 M - 1\(15649\)

## 문제

자연수 N과 M이 주어졌을 때, 아래 조건을 만족하는 길이가 M인 수열을 모두 구하는 프로그램을 작성하시오.

* 1부터 N까지 자연수 중에서 중복 없이 M개를 고른 수열

백트래킹에 대해서 검색해봄

백트래킹 방법을 사용해야하는데 백트래킹은 가능한 모든 방법을 탐색하는 방법이다.

대표적인 완전 탐색 방법으로는 DFS\(Depth First Search\)가 있다.

* 현재 지점에서 방문할 곳이 있으면 재귀 호출을 이용해서 계속 이동
* DFS의 장점은 무한히 깊은 곳을 찾아야할 떄 효율적
* 자기 자신을 호출하는 순환 알고리즘의 형태
* 그래프 탐색의 경우, 어떤 노드를 방문했었는지 여부를 반드시 검사해줘야한다!

dfs가 진행되는 순서는

하나의 노드에서 시작을 하게되고, 시작한 노드의 모든 인접한 노드를 차례로 순회한 다음, 만약 인접한 이웃 노드 b가 있다면 b 노드의 이웃 노드를 전부 방문해봐야한다

이렇게 이웃노드를 시작정점으로 dfs를 다시 시작해서 b의 이웃노드들을 방문한다

만약 b의 이웃노드들을 전부 탐색했다면 다시 처음의 시작 노드로 돌아가서 시작노드의 다른 이웃 노드를 찾는다

재귀호출을 사용하는 방법을 참고해서 문제를 풀어봄

재귀를 하면서 이미 방문한 값이라면 다음 값을 탐색하도록 하기 위해 M크기의 boolean 배열을 생성하고, 탐색과정에서 값에 담을 int 배열 arr을 생성한다.

그리고 depth을 통해서 재귀가 깊어질 때마다 depth을 1씩 증가시켜 M과 같아지면 더 이상 재귀를 호출하지 않고 탐색과정 중 값을 담았던 arr 배열을 출력해주고 리턴해주는 역할을 가지고 있음

## 입력

첫째 줄에 자연수 N과 M이 주어진다. \(1 ≤ M ≤ N ≤ 8\)

## 출력

한 줄에 하나씩 문제의 조건을 만족하는 수열을 출력한다. 중복되는 수열을 여러 번 출력하면 안되며, 각 수열은 공백으로 구분해서 출력해야 한다.

수열은 사전 순으로 증가하는 순서로 출력해야 한다.

## 예제 입력

```text
3 1
```

## 예제 출력

```text
1
2
3
```

## 예제 입력2

```text
4 2
```

## 예제 출력2

```text
1 2
1 3
1 4
2 1
2 3
2 4
3 1
3 2
3 4
4 1
4 2
4 3
```

## 예제 입력3

```text
4 4
```

## 예제 출력3

```text
1 2 3 4
1 2 4 3
1 3 2 4
1 3 4 2
1 4 2 3
1 4 3 2
2 1 3 4
2 1 4 3
2 3 1 4
2 3 4 1
2 4 1 3
2 4 3 1
3 1 2 4
3 1 4 2
3 2 1 4
3 2 4 1
3 4 1 2
3 4 2 1
4 1 2 3
4 1 3 2
4 2 1 3
4 2 3 1
4 3 1 2
4 3 2 1
```

## 풀이

```java
import java.io.*;
import java.util.*;

public class Main{
    public static int[] arr;
    public static boolean[] visit;
    public static int[] tmp;

    public static void main(String[] args){
        Scanner sc = new Scanner(System.in);
        int N = sc.nextInt();
        int M = sc.nextInt();

        arr = new int[M];
        visit = new boolean[N];
        dfs(N, M, 0);
    }

    public static void dfs(int N, int M, int depth){
        //재귀의 깊이가 M과 같아지면 탐색과정에 담았던 배열을 출력
        if(depth == M){

            for(int i=0; i<arr.length; i++){
                System.out.print(arr[i]+" ");
            }
            System.out.println();
            return;
        }

        for (int i = 0; i < N; i++) {
            //만약 아직 방문하지 않았다면
            if(!visit[i]){
                //해당 노드를 방문했다는 것으로 변경
                visit[i] = true;
                //깊이를 index로 해서 i+1값을 저장
                arr[depth] = i+1;
                //다음 자식 노드방문을 위해 깊이를 하나 늘려주면서 재귀 호출
                dfs(N, M, depth+1);
                //만약 자식 노드 방문이 끝나고 돌아오면 방문하지 않은 상태로 변경
                visit[i] = false;
            }
        }
    }
}
```

[백준](https://www.acmicpc.net/problem/15649)

