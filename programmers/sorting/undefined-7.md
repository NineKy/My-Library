# 가장 큰 수\(Lv2\)



**문제 설명**

0 또는 양의 정수가 주어졌을 때, 정수를 이어 붙여 만들 수 있는 가장 큰 수를 알아내 주세요.

예를 들어, 주어진 정수가 \[6, 10, 2\]라면 \[6102, 6210, 1062, 1026, 2610, 2106\]를 만들 수 있고, 이중 가장 큰 수는 6210입니다.

0 또는 양의 정수가 담긴 배열 numbers가 매개변수로 주어질 때, 순서를 재배치하여 만들 수 있는 가장 큰 수를 문자열로 바꾸어 return 하도록 solution 함수를 작성해주세요.

**제한 사항**

* numbers의 길이는 1 이상 100,000 이하입니다.
* numbers의 원소는 0 이상 1,000 이하입니다.
* 정답이 너무 클 수 있으니 문자열로 바꾸어 return 합니다.

**입출력 예**

| numbers | return |
| :--- | :--- |
| \[6, 10, 2\] | "6210" |
| \[3, 30, 34, 5, 9\] | "9534330" |



#### 

#### Java

Comparator을 사용했던거 기억 다시 되짚어보자

* 변수1.compareTo\(변수2\) : String타입인 변수1과 변수2의 사전순서대로 비교해주는 함수이고, 변수1에서 변수2를 뺀 값을 리턴해주는 함수이다.
  * 변수1 = 변수2 =&gt; 0
  * 변수1 &gt; 변수2 =&gt; 1
  * 변수1 &lt; 변수2 =&gt; -1

=&gt; 여기서 가정을 str1이 6 그리고 str2이 10이라고 해보자, 

만약에 차이가 compareTo의 값이 양수

1.  str2+str1이 106, str1+str2이 610이기 때문에 -1\(음수\)를 리턴해주고
2.  override한 compare함수에서 음수라고 판단하면 str2이 더 크다고 판단
3. 그러면 정상적으로 진행되고있음



```java
import java.util.Arrays;
import java.util.Comparator;
class Solution {
    public String solution(int[] numbers) {
        String answer = "";
        String[] arr = new String[numbers.length];
        for(int i=0; i<arr.length; i++){
            arr[i] = String.valueOf(numbers[i]);
        }
        
        Arrays.sort(arr, new Comparator<String>(){
            @Override
            public int compare(String str1, String str2){
                return (str2+str1).compareTo(str1+str2);
            }
        });
        if(arr[0].equals("0")){
            return "0";
        }
        for(String a: arr){
            answer += a;
        }
        return answer;
    }
}
```

