# 더 맵게

[https://programmers.co.kr/learn/courses/30/lessons/42626](https://programmers.co.kr/learn/courses/30/lessons/42626)

허허.. 문제는 어떻게든 풀었지만 사실 풀면서 뭔가 이상하다라는 생각을 하면서 풀었음....

맨 처음에 푼 코드는 어떻게든 실행만 되는 코드였다 → 테스트케이스는 모두 성공했지만 효율성에서 모두 시간초과가 발생...

```java
import java.util.*;
class Solution {
    public int solution(int[] scoville, int K) {
        int answer = 0;

        ArrayList<Integer> list = new ArrayList<>();
        for(int i=0; i<scoville.length; i++){
            list.add(scoville[i]);
        }

        while(!isGood(list, K) && list.size()>1){

            int min = getMin(list);
            list.remove(list.indexOf(min));
            int secMin = getMin(list);
            list.remove(list.indexOf(secMin));
            list.add(min + secMin*2);
            answer++;

        }

        if(!isGood(list, K)){
            answer = -1;
        }

        return answer;
    }

    public int getMin(ArrayList<Integer> list){
        int n=Integer.MAX_VALUE;
        for(int i=0; i<list.size(); i++){
            if(list.get(i)<n)
                n = list.get(i);
        }
        return n;
    }

    public boolean isGood(ArrayList<Integer> list, int k){
        for(int i=0; i<list.size(); i++){
            if(k>list.get(i))
                return false;
        }
        return true;
    }
}
```

그래서 이건 찾아보았다.. 감이 전혀 안오는걸?

자동으로 정렬해주는 Priority Queue을 사용하라.. 흠....

우선순위 큐는 우리가 기본적으로 알고 있는 큐와는 다르게 우선순위에 맞는 값을 먼저 나가게 해주는 자료구조이다.

결국은 참조를 통해서 풀었다... ㅠ 우선순위 큐에 대해서 조금 더 신경써서 기억해보자

```java
import java.util.*;
class Solution {
    public int solution(int[] scoville, int K) {
        int answer = 0;

        PriorityQueue<Integer> queue = new PriorityQueue<Integer>(scoville.length);

        for(int num : scoville){
            queue.add(num);
        }

        while(queue.size() > 1){
            if(queue.peek()>=K){
                return answer;
            }
            int min = queue.poll();
            int secmin = queue.poll();
            queue.add(min + (secmin*2));
            answer++;
        }

        if(queue.peek()<K)
            return -1;

        return answer;
    }
}
```
