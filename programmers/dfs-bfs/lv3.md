# 네트워크\(Lv3\)

**문제 설명**

네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

**제한사항**

* 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
* 각 컴퓨터는 0부터 `n-1`인 정수로 표현합니다.
* i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers\[i\]\[j\]를 1로 표현합니다.
* computer\[i\]\[i\]는 항상 1입니다.

**입출력 예**

| n | computers | return |
| :--- | :--- | :--- |
| 3 | \[\[1, 1, 0\], \[1, 1, 0\], \[0, 0, 1\]\] | 2 |
| 3 | \[\[1, 1, 0\], \[1, 1, 1\], \[0, 1, 1\]\] | 1 |

**입출력 예 설명**

예제 \#1  
아래와 같이 2개의 네트워크가 있습니다.  
![image0.png](https://grepp-programmers.s3.amazonaws.com/files/ybm/5b61d6ca97/cc1e7816-b6d7-4649-98e0-e95ea2007fd7.png)

예제 \#2  
아래와 같이 1개의 네트워크가 있습니다.  
![image1.png](https://grepp-programmers.s3.amazonaws.com/files/ybm/7554746da2/edb61632-59f4-4799-9154-de9ca98c9e55.png)



```java
        //이걸로 해보니까 테스트 2개는 잘 통과했지만 제출하니까 바로 틀림 
        //=> 방문했던거를 기록해야 했나봐
         int answer = 0;
         for(int i=0; i<n-1; i++){
             for(int j=i+1; j<n; j++){
                 if(computers[i][j] == 1){
                        answer++;
                }
            }
        }
        return n-answer;
```



처음에 생각했던건 점화식을 만들지 않았다. 그리구 방문을 체크하지 않았던게 문제였다고 생각한다

아직도 dfs를 잘 못만드는 거 같다..! 그리고 딱보면 딱 알아야하지만 그런 눈도 좀 부족한거같음

```java
class Solution {
    int answer = 0;
    public int solution(int n, int[][] computers) {  
        
        boolean [] visited = new boolean[n];
        
        for(int i=0; i<n; i++){
            if(visited[i] == false){
                answer++;
                recur(i, visited, computers);
            }
        }
        return answer;
    }
    
    public void recur(int index, boolean[] visited, int[][] computers){
        visited[index] = true;
        for(int i=0; i<computers.length; i++){
            if(computers[index][i] == 1 && i != index && visited[i]==false){
                recur(i, visited, computers);
            }
        }
    }
}
```





## 2번째 시도

시도 자체는 진행이 잘 되어가고 있었는데, 네트워크의 갯수를 찾는 과정에서 잘 모르겠어서 고생을 좀 했다... 결국은 답을 찾아봤는데, 

접근 하는 방향을 같다고 볼 수 있었다. 세로를 중심으로 연속된 것들이 같은 숫자를 가리키면 연결되게 하는 방식 -&gt; 기존에 본인은 2차원 배열으로 visited을 가지고 검사를 진행했었는데, 하나의 배열을 가지고 진행을 해보았다. 각 컴퓨터를 나타내는 방식이다

그래서 만약 방문 체크를 하지 않았으면서, 컴퓨터끼리의 방향은 가있다면 다음 재귀로 넘어가는 방식으로 구현을 해두었다. 뭔가 아쉽다. 다 온거같은데 실패해서 너무 아쉬웠다.

```java
class Solution {
    boolean[] visited;
    int answer = 0;
    public int solution(int n, int[][] computers) {
        visited = new boolean[n];
        for(int i=0; i<n; i++){
            if(!visited[i]){
                fn(n, computers, i);
                answer++;
            }
        }
        return answer;
    }
    
    public void fn(int n, int[][] computers, int depth){
        
        for(int i=0; i<n; i++){
            visited[depth] = true;
            if(!visited[i] && computers[depth][i] == 1){
                fn(n, computers, i);
            }
        }
    }
}
```



