# 위장

해쉬맵을 사용해서 푸는 문제였다

HashMap vs Map에 대해서 햇갈렸었는데 → 이게 더욱 자세하게 알아봐야겠지만 일단 hashmap보다 map이 더욱 상위 인터페이스이다. = 대충 나처럼 라이트한 유저에게는 hashmap이 사용하기 더 편할것같다

```java
class Solution {
    public int solution(String[][] clothes) {
        int answer = 1;

        HashMap<String, Integer> map = new HashMap<>();
        for(int i=0; i<clothes.length; i++){
            String type = clothes[i][1];
            if(!map.containsKey(type)){
                map.put(type, 1);
            }else{
                int n = map.get(type);
                map.put(type, n+1);
            }
        }

        for(int i : map.values()){
            answer*=(i+1);
        }

        return answer-1;
    }
}
```
