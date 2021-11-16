# 신규 아이디 추천

[https://programmers.co.kr/learn/courses/30/lessons/72410](https://programmers.co.kr/learn/courses/30/lessons/72410)

카카오 기출 문제이지만 레벨 1이지만서도.. 솔직히 막혔었다....

연속된 마침표 처리부분에서 좀 헤맸지만 해결!

```java
class Solution {
    public String solution(String new_id) {
        String answer = "";

        answer = new_id.toLowerCase();

        String tmp = "";
        for (int i = 0; i < answer.length(); i++) {
            char c = answer.charAt(i);
            if ((c >= 'a' && c <= 'z') || (c >= '0' && c <= '9') || c == '-' || c == '_' || c == '.')
                tmp += String.valueOf(c);
        }
        answer = tmp;

        answer = answer.replace("..", ".");
        while (answer.contains(".."))
            answer = answer.replace("..", ".");

        if(answer.length() > 0){
            if (answer.charAt(0) == '.')
                answer = answer.substring(1, answer.length());
        }

        if(answer.length() > 0){
            if (answer.charAt(answer.length() - 1) == '.' && answer.length() > 0)
                answer = answer.substring(0, answer.length() - 1);
        }

        if (answer.equals(""))
            answer = "a";

        if (answer.length() >= 16) {
            answer = answer.substring(0, 15);
            if (answer.charAt(answer.length() - 1) == '.')
                answer = answer.substring(0, answer.length() - 1);
        }

        if (answer.length() <= 2) {
            while (answer.length() < 3) {
                answer += answer.charAt(answer.length() - 1);
            }
        }
        return answer;
    }
}
```
