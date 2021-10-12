---
description: JS/탐욕법/배열요소더하기(String)/배열요소리턴
---

# 큰 수 만들기\(Lv2\)

**문제 설명**

어떤 숫자에서 k개의 수를 제거했을 때 얻을 수 있는 가장 큰 숫자를 구하려 합니다.

예를 들어, 숫자 1924에서 수 두 개를 제거하면 \[19, 12, 14, 92, 94, 24\] 를 만들 수 있습니다. 이 중 가장 큰 숫자는 94 입니다.

문자열 형식으로 숫자 number와 제거할 수의 개수 k가 solution 함수의 매개변수로 주어집니다. number에서 k 개의 수를 제거했을 때 만들 수 있는 수 중 가장 큰 숫자를 문자열 형태로 return 하도록 solution 함수를 완성하세요.

**제한 조건**

* number는 1자리 이상, 1,000,000자리 이하인 숫자입니다.
* k는 1 이상 `number의 자릿수` 미만인 자연수입니다.

**입출력 예**

| number | k | return |
| :--- | :--- | :--- |
| "1924" | 2 | "94" |
| "1231234" | 3 | "3234" |
| "4177252841" | 4 | "775841" |

[출처](http://hsin.hr/coci/archive/2011_2012/contest4_tasks.pdf)



탐욕법을 사용, 스택방식을 사용

arr.join\(""\) : 배열의 요소들을 하나의 문자열로 묶어줌

.substr\(a, b\) : 배열의의 a 부터 b번째 자리수까지 리턴

```javascript
function solution(number, k) {
    var answer = '';
    let count=0;
    let arr = [];
    
    for(let i=0; i<number.length; i++){
        while(k>0 && arr.length>0 && arr[arr.length-1]<number[i]){
            arr.pop();
            k--;
        }
        arr.push(number[i]);
    }
    answer = arr.join("").substr(0, number.length-k);
    return answer;
}
```



### Java

레벨 2인데도 잘 못풀겠네... 열심히 해봅시당....

[https://moon1226.tistory.com/67](https://moon1226.tistory.com/67) &lt;- 좋은 글 감사합니다 :\)

문제의 해설이라고하긴 뭣하지만 방식은 큰 값의 자리수를 지키는 한에서 돌아가면서 숫자의 최대값을 찾아가면서 최대값을 더해가는 방식이다.

```java
class Solution {
    public String solution(String number, int k) {
        //String answer = "";
        StringBuilder sb = new StringBuilder();
        
        char max;
        //index변수로 최대값을 찾으면 그 이후부터 찾도록 나누는 분기점 역할!
        int index=0;
        
        //결과 값(숫자)의 자리 수만큼 돈다
        for(int i=0; i<number.length()-k; i++){
            //최대값을 확인하기 위해서는 for문이 돌때마다 초기화를 시켜줘야하고
            max = '0';
            
            for(int j=index; j<=k+i; j++){
                if(max < number.charAt(j)){
                    //최대값을 찾았으면 최대값으로 바꿔주고 
                    max = number.charAt(j);
                    //다음부터 다시 찾기 시작
                    index = j+1;
                }
            }
            //최대값이 확정 -> answer에 concat을 이용해서 붙히자
            //answer = answer.concat(Character.toString(max));
            sb.append(max);
        }
        //return answer;
        return sb.toString();
    }
}
```

하나의 테스트에서 시간 초과가 났다. 하나에서 시간초과가 났다는 것은 코드적인 문제보다는 그냥 string을 concat하는 과정이지 않을까 싶다.

그래서 참고한분의 코드를 보니 StringBuilder을 사용하셨더라, 이건 옛날에도 알고 있던 사실이였지만 시간을 조금이라도 줄일 수 있는 방법이다!

