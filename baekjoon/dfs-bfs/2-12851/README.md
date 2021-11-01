# 숨바꼭질 2(12851)

[https://www.acmicpc.net/problem/12851](https://www.acmicpc.net/problem/12851)

## 문제

수빈이는 동생과 숨바꼭질을 하고 있다. 수빈이는 현재 점 N(0 ≤ N ≤ 100,000)에 있고, 동생은 점 K(0 ≤ K ≤ 100,000)에 있다. 수빈이는 걷거나 순간이동을 할 수 있다. 만약, 수빈이의 위치가 X일 때 걷는다면 1초 후에 X-1 또는 X+1로 이동하게 된다. 순간이동을 하는 경우에는 1초 후에 2\*X의 위치로 이동하게 된다.

수빈이와 동생의 위치가 주어졌을 때, 수빈이가 동생을 찾을 수 있는 가장 빠른 시간이 몇 초 후인지 그리고, 가장 빠른 시간으로 찾는 방법이 몇 가지 인지 구하는 프로그램을 작성하시오.

## 입력

첫 번째 줄에 수빈이가 있는 위치 N과 동생이 있는 위치 K가 주어진다. N과 K는 정수이다.

## 출력

첫째 줄에 수빈이가 동생을 찾는 가장 빠른 시간을 출력한다.

둘째 줄에는 가장 빠른 시간으로 수빈이가 동생을 찾는 방법의 수를 출력한다.

## 예제 입력 1

```java
5 17
```

## 예제 출력 1

```java
4
2
```

## 풀이

아쉽게도 100% 다 풀지는 못했다.. ㅠㅠ... 다시 시간나면 풀어보자 충분히 할 수 있을것 같다!

```java
import java.util.*;
import java.io.*;

public class Main {
    static boolean[] visited = new boolean[100001];
    static int cnt[] = new int[100001];
    static int dist[] = new int[100001];

    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine(), " ");
        int n = Integer.parseInt(st.nextToken());
        int m = Integer.parseInt(st.nextToken());
        fn(n, m);

    }

    public static void fn(int start, int end) {
        Queue<Integer> q = new LinkedList<>();
        q.add(start);
        visited[start] = true;
        cnt[start] = 1;
        while (!q.isEmpty()) {
            int tmp = q.poll();
            int[] cal = {tmp - 1, tmp + 1, tmp * 2};
            for (int i = 0; i < 3; i++) {
                int t = cal[i];
                if (t >= 0 && t <= 100000) {
                    if (!visited[t]) {
                        visited[t] = true;
                        dist[t] = dist[tmp] + 1;
                        q.add(t);
                        cnt[t] = cnt[tmp];
                    } else if (dist[t] == dist[tmp] + 1) {
                        cnt[t] += cnt[tmp];
                    }
                }

            }
        }
        System.out.println(dist[end]);
        System.out.println(cnt[end]);
    }
}
```
