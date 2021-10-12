# 정수 삼각형\(Lv3\)



**문제 설명**

![&#xC2A4;&#xD06C;&#xB9B0;&#xC0F7; 2018-09-14 &#xC624;&#xD6C4; 5.44.19.png](https://grepp-programmers.s3.amazonaws.com/files/production/97ec02cc39/296a0863-a418-431d-9e8c-e57f7a9722ac.png)

위와 같은 삼각형의 꼭대기에서 바닥까지 이어지는 경로 중, 거쳐간 숫자의 합이 가장 큰 경우를 찾아보려고 합니다. 아래 칸으로 이동할 때는 대각선 방향으로 한 칸 오른쪽 또는 왼쪽으로만 이동 가능합니다. 예를 들어 3에서는 그 아래칸의 8 또는 1로만 이동이 가능합니다.

삼각형의 정보가 담긴 배열 triangle이 매개변수로 주어질 때, 거쳐간 숫자의 최댓값을 return 하도록 solution 함수를 완성하세요.

**제한사항**

* 삼각형의 높이는 1 이상 500 이하입니다.
* 삼각형을 이루고 있는 숫자는 0 이상 9,999 이하의 정수입니다.

**입출력 예**

| triangle | result |
| :--- | :--- |
| \[\[7\], \[3, 8\], \[8, 1, 0\], \[2, 7, 4, 4\], \[4, 5, 2, 6, 5\]\] | 30 |



이전에 백준에서 풀었던 문제였지만 생각보다 쉽게 풀리지는 않았다... =&gt; 범위 부분에서 저번에 푼것과는 좀 달라서 시간투자가 좀 됬다...

내려가면서 합을 계산하고 최대값을 골라서 적으면서 내려가면 맨 마지막에 최대값을 적어둔다.

그리고 맨 마지막 라인에서 최대값을 찾으면? 그게 최대값이자나!

```java
import java.lang.Math;
class Solution {
    public int solution(int[][] triangle) {
        int answer = 0;
        
        for(int i=1; i<triangle.length; i++){
            for(int j=0; j<=i; j++){
                if(j==0)
                    triangle[i][j] += triangle[i-1][j];
                else if(i==j)
                    triangle[i][j] += triangle[i-1][j-1];
                else
                    triangle[i][j] += Math.max(triangle[i-1][j-1], triangle[i-1][j]);
            }
        }
        
        for(int i=0; i<triangle.length; i++){
            if(answer < triangle[triangle.length-1][i])
                answer = triangle[triangle.length-1][i];
        }
        
        return answer;
    }
}
```



