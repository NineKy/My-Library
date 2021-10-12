# 다음 큰 숫자

**문제 설명**

자연수 n이 주어졌을 때, n의 다음 큰 숫자는 다음과 같이 정의 합니다.

* 조건 1. n의 다음 큰 숫자는 n보다 큰 자연수 입니다.
* 조건 2. n의 다음 큰 숫자와 n은 2진수로 변환했을 때 1의 갯수가 같습니다.
* 조건 3. n의 다음 큰 숫자는 조건 1, 2를 만족하는 수 중 가장 작은 수 입니다.

예를 들어서 78\(1001110\)의 다음 큰 숫자는 83\(1010011\)입니다.

자연수 n이 매개변수로 주어질 때, n의 다음 큰 숫자를 return 하는 solution 함수를 완성해주세요.

**제한 사항**

* n은 1,000,000 이하의 자연수 입니다.

**입출력 예**

| n | result |
| :--- | :--- |
| 78 | 83 |
| 15 | 23 |

**입출력 예 설명**

입출력 예\#1  
문제 예시와 같습니다.  
입출력 예\#2  
15\(1111\)의 다음 큰 숫자는 23\(10111\)입니다.



```java
class Solution {
    public int solution(int n) {
        int answer = 0;
        
        //이진수로 어떻게 나눌 것이냐 -> 함수가 있넹
        String bin = Integer.toBinaryString(n);
        String tmp[] = bin.split("");
        
        int iCnt = 0;
        for(int i=0; i<tmp.length; i++){
            if(tmp[i].equals("1"))
                iCnt++;
        }
        
        for(int i=n+1; i<1000000;i++){
            String tb = Integer.toBinaryString(i);
            String ta[] = tb.split("");
            int c=0;
            for(int j=0; j<ta.length; j++){
                if(ta[j].equals("1"))
                    c++;
            }
            if(c==iCnt){
                answer = i;
                break;
            }
                
        }
        
        return answer;
    }
}
```

테스트 케이스는 모두 통과했지만

하지만 Integer.bitCount\(\)라는 메소드를 활용하면 너무 쉽게 풀리는 문제였다 -&gt; 이건 입력된 정수를 2진수로 변환한 뒤, 2진수에 포함된 1을 세주는 메소드이다



```java
class Solution {
    public int solution(int n) {
        int answer = 0;
        
        int o_count = Integer.bitCount(n);
        
        int c = 0;
        while(true){
            n++;
            c = Integer.bitCount(n);
            if(c == o_count){
                answer = n;
                break;
            }
        }
        
        return answer;
    }
}
```

