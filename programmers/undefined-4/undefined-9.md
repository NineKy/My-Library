# 카펫\(Lv2\)

완전 탐색을 이용해야한다.

**문제 설명**

Leo는 카펫을 사러 갔다가 아래 그림과 같이 중앙에는 노란색으로 칠해져 있고 테두리 1줄은 갈색으로 칠해져 있는 격자 모양 카펫을 봤습니다.

![carpet.png](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/b1ebb809-f333-4df2-bc81-02682900dc2d/carpet.png)

Leo는 집으로 돌아와서 아까 본 카펫의 노란색과 갈색으로 색칠된 격자의 개수는 기억했지만, 전체 카펫의 크기는 기억하지 못했습니다.

Leo가 본 카펫에서 갈색 격자의 수 brown, 노란색 격자의 수 yellow가 매개변수로 주어질 때 카펫의 가로, 세로 크기를 순서대로 배열에 담아 return 하도록 solution 함수를 작성해주세요.

**제한사항**

* 갈색 격자의 수 brown은 8 이상 5,000 이하인 자연수입니다.
* 노란색 격자의 수 yellow는 1 이상 2,000,000 이하인 자연수입니다.
* 카펫의 가로 길이는 세로 길이와 같거나, 세로 길이보다 깁니다.

**입출력 예**

| brown | yellow | return |
| :--- | :--- | :--- |
| 10 | 2 | \[4, 3\] |
| 8 | 1 | \[3, 3\] |
| 24 | 24 | \[8, 6\] |

[출처](http://hsin.hr/coci/archive/2010_2011/contest4_tasks.pdf)



#### Javascript

큰것부터 내려가야하는 점이랑 

가운데의 색깔인 노란색은 가운데에 들어가기 때문에 가로와 세로에서 2씩 뺀 숫자만큼의 크기이니까 마지막에 이걸 확인해서 리턴

```javascript
function solution(brown, yellow) {
    var answer = [];
    let sum = brown+yellow;
    for(let i=Math.floor(sum/2); i>0; i--){
        if(sum%i !== 0)
            continue;
        let w = i;
        let h = sum/i;
        
        if((w-2)*(h-2) == yellow)
            return [w,h];
    }
    
    return answer;
}
```



### Java

js방식이나 java방식이나 같다! 

가로, 세로의 최대값이 될 수 있는 수는 최대가 넓이에서 2로 나눈 값이기 때문에 for문의 시작을 sum/2에서 부터 시작해서 하나씩 줄여나가는 방법을 사용했음

각 가로와 세로가 대충 알게 되었을 때, 노란색은 무조건 갈색에 덮혀있어야하기 때문에 덮혔다는 의미인 -2을 해서 확실히 노란색이 갈색에 덮혔는지 확인하고 맞다면 그 경우의 수밖에 없기 때문에 answer배열에 넣어주고 리턴



```java
class Solution {
    public int[] solution(int brown, int yellow) {
        int[] answer = {0,0};
        int sum = brown + yellow;
        for(int i=sum/2; i>0; i--){
            if((sum%i) != 0) continue;
            int w = i;
            int h = sum/i;
            
            if((w-2)*(h-2) == yellow){
                answer[0] = h;
                answer[1] = w;
            }
        }

        return answer;
    }
}
```



