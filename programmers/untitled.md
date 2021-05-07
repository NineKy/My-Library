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

```text
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

