---
description: 'JS 문자열, 숫자비교'
---

# 문자열 다루기 기본\(Lv1\)



**문제 설명**

문자열 s의 길이가 4 혹은 6이고, 숫자로만 구성돼있는지 확인해주는 함수, solution을 완성하세요. 예를 들어 s가 "a234"이면 False를 리턴하고 "1234"라면 True를 리턴하면 됩니다.

**제한 사항**

* `s`는 길이 1 이상, 길이 8 이하인 문자열입니다.

**입출력 예**

| s | return |
| :--- | :--- |
| "a234" | false |
| "1234" | true |



어떤 방법이 가장 좋을것인지 고민해봤는데 parseInt를 통해서 정상적으로 변경이되면 숫자라는 뜻이니까 parseInt\(\)을 사용했다

```text
function solution(s) {
    let len = parseInt(s);
    if((s.length === 4 || s.length === 6) && len == s){
        len = true;
    }else
        len = false;
    return len;
}
```

