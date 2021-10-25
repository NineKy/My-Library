# A->B (16953)

[https://www.acmicpc.net/problem/16953](https://www.acmicpc.net/problem/16953)

## 문제

정수 A를 B로 바꾸려고 한다. 가능한 연산은 다음과 같은 두 가지이다.

* 2를 곱한다.
* 1을 수의 가장 오른쪽에 추가한다.

A를 B로 바꾸는데 필요한 연산의 최솟값을 구해보자.

## 입력

첫째 줄에 A, B (1 ≤ A < B ≤ 109)가 주어진다.

## 출력

A를 B로 바꾸는데 필요한 연산의 최솟값에 1을 더한 값을 출력한다. 만들 수 없는 경우에는 -1을 출력한다.

## 예제 입력 1

```sql
2 162
```

## 예제 출력 1

```sql
5
```

## 예제 입력 2

```sql
4 42
```

## 예제 출력 2

```sql
-1
```

## 예제 입력 3

```sql
100 40021
```

## 예제 출력 3

```sql
5
```

문제의 구현은 어렵지 않았지만 역시 막혔던 부분은 타입에서 막혔다.

10의 9승을 준다고 했는데, 저정도의 수가 들어와서 결국 로직을 타게 된다면 숫가 엄청 불어날 뿐만아니라 기본적으로 int 의 크기가 -20억부터 20억까지라고 알고 있다 → 9자리수가 1억인데 int의 범위는 10자리수

그럼 20억이 들어와서 값을 돌게되면 int의 범위를 넘어가기 때문에 Long 타입을 사용해서 풀어야 했다!!

DFS

```java
import java.util.*;
import java.io.*;
public class Main{
    static long start, end;
    static int result = -1;
    public static void main(String[] args)throws Exception{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine(), " ");
        start = Integer.parseInt(st.nextToken());
        end = Integer.parseInt(st.nextToken());
        fn(start, 0);
        System.out.println(result);
    }

    public static void fn(long num, int count){
        count++;
        if(num == end){
            result = count;
            return;
        }

        if(num*2<=end){
            fn(num*2, count);
        }
        if(num*10+1<=end){
            fn(num*10+1, count);
        }

    }
}
```

BFS

```java
import java.util.*;
import java.io.*;
public class Main{
    static long result;
    public static void main(String[] args)throws Exception{
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine(), " ");
        long start = Long.parseLong(st.nextToken());
        long end = Long.parseLong(st.nextToken());
        fn(start, end, 0);
        System.out.println(result);
    }

    public static void fn(long start, long end, long count){
        Queue<Long[]> q = new LinkedList<>();
        q.add(new Long[]{start, count+1});
        while(!q.isEmpty()){
            Long[] tmp = q.poll();
            long num = tmp[0];
            long cnt = tmp[1];
            if(num == end){
                result = cnt;
                return;
            }
            if(num*2<=end){
                q.add(new Long[]{num*2, cnt+1});
            }
            if(num*10+1<=end){
                q.add(new Long[]{num*10+1, cnt+1});
            }
        }
        result = -1;
    }
}
```
