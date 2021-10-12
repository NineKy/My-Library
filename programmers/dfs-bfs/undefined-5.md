# 타겟 넘버\(Lv2\)

**문제 설명**

n개의 음이 아닌 정수가 있습니다. 이 수를 적절히 더하거나 빼서 타겟 넘버를 만들려고 합니다. 예를 들어 \[1, 1, 1, 1, 1\]로 숫자 3을 만들려면 다음 다섯 방법을 쓸 수 있습니다.

```text
-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3
```

사용할 수 있는 숫자가 담긴 배열 numbers, 타겟 넘버 target이 매개변수로 주어질 때 숫자를 적절히 더하고 빼서 타겟 넘버를 만드는 방법의 수를 return 하도록 solution 함수를 작성해주세요.

**제한사항**

* 주어지는 숫자의 개수는 2개 이상 20개 이하입니다.
* 각 숫자는 1 이상 50 이하인 자연수입니다.
* 타겟 넘버는 1 이상 1000 이하인 자연수입니다.

**입출력 예**

| numbers | target | return |
| :--- | :--- | :--- |
| \[1, 1, 1, 1, 1\] | 3 | 5 |

**입출력 예 설명**

문제에 나온 예와 같습니다.

### Javascript

```javascript
function solution(numbers, target) {
    var answer = 0;
    function recur(index, sum){
        if(index === numbers.length){
            if(sum===target){
                answer++;
            }
            return ;
        }
        recur(index+1, sum+numbers[index]);
        recur(index+1, sum-numbers[index]);
    }
    recur(0,0);
    return answer;
}
```



DFS 문제는 재귀나 Stack을 사용해서 문제를 풀이한다.

DFS는 깊이 우선 탐색이고 / 배열의 요소 : 하나의 노드 / 배열의 인덱스 : 깊이

DFS에서는 점화식과 종료조건을 찾는 것이 중요! 

*  종료 조건은 배열의 모든 요소를 접근했을 떄의 경우
* 점화식은 인덱스의 요소를 더할지, 더하지 않을 지의 모든 경우를 구함



모든 경우의수 \(+,-\)을 계산해서 해당하는 숫자가 나오는 경우를 재귀함수를 통해서 찾는다

index값으로 체크해가면서 재귀함수를 돌리는데 

### Java

```java
class Solution {
    int answer = 0;
    public int solution(int[] numbers, int target) {
        
        int answer = recur(numbers, 0, target, 0);
        return answer;
    }
    
    public int recur(int[] numbers, int index, int target, int sum){
        if(index == numbers.length){
            if(sum == target){
                return 1;
            }
            return 0;
        }
        int ans=0;
        ans += recur(numbers, index+1, target, sum+numbers[index]);
        ans += recur(numbers, index+1, target, sum-numbers[index]);
        return ans;
    }
}
```

어떤 노드를 기준으로 나머지 숫자들을 모두 확인하는 방법으로 진행할꺼기 때문에 index라는 변수를 넣어서 진행하면서 1씩 늘려갔고

실제로 0번째 기준으로 경우의 수를 따져보니, +인경우와 -인경우를 따로 계산해줘야 해서 재귀에서 넘어갈 시, sum+numbers와 sum-numbers 이렇게 나눠서 재귀를 돌렸다.

이렇게 점화식을 만들었고, 종료시점은 index을 기준으로 만약 끝까지 모든 노드를 순회했다면\(==numbers.length\) 종료하게 되어있고, 그때의 총 계산 값이 입력받은 target과 같다면, 계산을 1더해준다.



## 2번째 시도

그렇게 어렵지 않은걸? 2단계라 그런가 ㅋㅋㄹㅋㅋ

```java
class Solution {
    int answer = 0;
    public int solution(int[] numbers, int target) {  
        fn(numbers, target, 0, 0);
        return answer;
    }
    
    public void fn(int[] numbers, int target, int depth, int sum){
        if(depth == numbers.length){
            if(sum == target){
                answer++;
            }
            return;
        }
        
        fn(numbers, target, depth+1, sum + numbers[depth]);
        fn(numbers, target, depth+1, sum - numbers[depth]);
        
    }
}
```







