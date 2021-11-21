# 오픈채팅방

[https://programmers.co.kr/learn/courses/30/lessons/42888](https://programmers.co.kr/learn/courses/30/lessons/42888)

2단계 부수기 진행중..

어렵지 않게 구현할 수 있었는데 map을 사용하는 과정에서 궁금증이 생김 → hashmap vs map?

```java
import java.util.*;
class Solution {
    public String[] solution(String[] record) {
        String[] answer = {};

        ArrayList<String> arr = new ArrayList<>();
        HashMap<String, String> map = new HashMap<>();
        
        for(String s : record){
            StringTokenizer st = new StringTokenizer(s);
            String behave = st.nextToken();
            String uid = st.nextToken();
            String name = "";
            if(!behave.equals("Leave")){
                name = st.nextToken();
            }
            
            switch(behave){
                case "Enter":
                    map.put(uid, name);
                    arr.add(uid+"님이 들어왔습니다.");
                    break;
                case "Leave":
                    arr.add(uid+"님이 나갔습니다.");
                    break;
                case "Change":
                    map.put(uid, name);
                    break;
            }
        }
        
        answer = new String[arr.size()];
        
        int ansidx = 0;
        for(String s : arr){
            int idx = s.indexOf("님");
            String id = s.substring(0, idx);
            answer[ansidx] = s.replace(id, map.get(id));
            ansidx++;
        }

        return answer;
    }
}
```
