# 짝지어 제거하기

**문제 설명**

짝지어 제거하기는, 알파벳 소문자로 이루어진 문자열을 가지고 시작합니다. 먼저 문자열에서 같은 알파벳이 2개 붙어 있는 짝을 찾습니다. 그다음, 그 둘을 제거한 뒤, 앞뒤로 문자열을 이어 붙입니다. 이 과정을 반복해서 문자열을 모두 제거한다면 짝지어 제거하기가 종료됩니다. 문자열 S가 주어졌을 때, 짝지어 제거하기를 성공적으로 수행할 수 있는지 반환하는 함수를 완성해 주세요. 성공적으로 수행할 수 있으면 1을, 아닐 경우 0을 리턴해주면 됩니다.

예를 들어, 문자열 S = `baabaa` 라면

b _aa_ baa → _bb_ aa → _aa_ →

의 순서로 문자열을 모두 제거할 수 있으므로 1을 반환합니다.

**제한사항**

* 문자열의 길이 : 1,000,000이하의 자연수
* 문자열은 모두 소문자로 이루어져 있습니다.

**입출력 예**

| s | result |
| :--- | :--- |
| baabaa | 1 |
| cdcd | 0 |

**입출력 예 설명**

입출력 예 \#1  
위의 예시와 같습니다.  
입출력 예 \#2  
문자열이 남아있지만 짝지어 제거할 수 있는 문자열이 더 이상 존재하지 않기 때문에 0을 반환합니다.



```java
import java.util.*;
class Solution
{
    public int solution(String s)
    {
        int answer = 0;
        
        //2개가 붙어있는 짝을 찾고
        //그 2개를 제거하고 앞뒤로 이어붙힘
        //짝지어서 문자가 남지 않는다면 1리턴 불가능하면 0리턴하는 문제
        
        String tmp[] = s.split("");
        ArrayList<String> list = new ArrayList<>();
        for(int i=0; i<tmp.length; i++){
            list.add(tmp[i]);
        }
        //012
        int i=1;
        while(i<list.size()){
            if(list.size()>1 && list.get(i-1).equals(list.get(i))){
                //다 잘했는데 여기서 시간을 좀 끌렸음 ㅠ
                //list를 remove하면 자동으로 뒤에숫자가 정렬되는데 그러면 
                //i-1과 i을 remove 하는게 아니라 
                //i과 i을 remove해줘야 원하는대로 삭제 가능!
                list.remove(i-1);
                list.remove(i-1);
                i=1; 
                if(list.isEmpty())
                    return 1;
            }
            
            else{
                i++;
            }
        }
        
        return answer;
    }
}
```

이렇게 풀었을때는 테스트 케이스는 통과하지만 효율성에서 시간이 초과된다!!

그래서 스택을 활용해보았다

```java
import java.util.*;
class Solution
{
    public int solution(String s)
    {
        int answer = 0;
        
        Stack<Character> stack = new Stack<>();
        char arr[] = s.toCharArray();
        for(int i=0; i<arr.length; i++){
            if(stack.isEmpty()){
                stack.push(arr[i]);
            }else{
                if(stack.peek().equals(arr[i])){
                    stack.pop();
                }else{
                    stack.push(arr[i]);
                }
            }
        }
        
        if(stack.size() == 0)
            return 1;
        
        return answer;
    }
}

```

