---
title: '1011'
excerpt: Fly me to the Alpha Centauri
categories:
  - 백준
description: 거리에 따른 장치 사용 횟수를 출력하는 문제
---

# Fly me to the Alpha Centauri\(1011\)

## 문제

우현이는 어린 시절, 지구 외의 다른 행성에서도 인류들이 살아갈 수 있는 미래가 오리라 믿었다. 그리고 그가 지구라는 세상에 발을 내려 놓은 지 23년이 지난 지금, 세계 최연소 ASNA 우주 비행사가 되어 새로운 세계에 발을 내려 놓는 영광의 순간을 기다리고 있다.

그가 탑승하게 될 우주선은 Alpha Centauri라는 새로운 인류의 보금자리를 개척하기 위한 대규모 생활 유지 시스템을 탑재하고 있기 때문에, 그 크기와 질량이 엄청난 이유로 최신기술력을 총 동원하여 개발한 공간이동 장치를 탑재하였다. 하지만 이 공간이동 장치는 이동 거리를 급격하게 늘릴 경우 기계에 심각한 결함이 발생하는 단점이 있어서, 이전 작동시기에 k광년을 이동하였을 때는 k-1 , k 혹은 k+1 광년만을 다시 이동할 수 있다. 예를 들어, 이 장치를 처음 작동시킬 경우 -1 , 0 , 1 광년을 이론상 이동할 수 있으나 사실상 음수 혹은 0 거리만큼의 이동은 의미가 없으므로 1 광년을 이동할 수 있으며, 그 다음에는 0 , 1 , 2 광년을 이동할 수 있는 것이다. \( 여기서 다시 2광년을 이동한다면 다음 시기엔 1, 2, 3 광년을 이동할 수 있다. \)

![img](https://www.acmicpc.net/upload/201003/rlaehdgur.JPG)

김우현은 공간이동 장치 작동시의 에너지 소모가 크다는 점을 잘 알고 있기 때문에 x지점에서 y지점을 향해 최소한의 작동 횟수로 이동하려 한다. 하지만 y지점에 도착해서도 공간 이동장치의 안전성을 위하여 y지점에 도착하기 바로 직전의 이동거리는 반드시 1광년으로 하려 한다.

김우현을 위해 x지점부터 정확히 y지점으로 이동하는데 필요한 공간 이동 장치 작동 횟수의 최솟값을 구하는 프로그램을 작성하라.

## 입력

입력의 첫 줄에는 테스트케이스의 개수 T가 주어진다. 각각의 테스트 케이스에 대해 현재 위치 x 와 목표 위치 y 가 정수로 주어지며, x는 항상 y보다 작은 값을 갖는다. \(0 ≤ x &lt; y &lt; 231\)

## 출력

각 테스트 케이스에 대해 x지점으로부터 y지점까지 정확히 도달하는데 필요한 최소한의 공간이동 장치 작동 횟수를 출력한다.

## 예제 입력

```text
3
0 3
1 5
45 50
```

## 예제 출력

```text
3
3
4
```

이것도.. 도움을 자주 받는 블로그를 참고해서 진행했다...ㅠ 너무어렵네

이러한 문제는 표나 그림을 그려서 나열해보면 규칙을 찾을 수 있다

규칙 1. max의 값은 d의 루트 값에서 소수점을 버린 정수값이랑 같다

max = \(int\) Math.sqrt\(d\)

자바에서 루트를 구하는 메소드는 Math.sqrt\(\), 리턴값은 더블

규칙 2. max가 변하는 지점과 다음 지점 사이에는 항상 count가 두 번씩 변함

규칙 3. max 값이 변할 때 count는

count = 2\*max-1 수식을 따른다

## 풀이

```java
import java.io.*;
import java.util.*;

public class Main{
    public static void main(String[] args){
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringBuilder sb = new StringBuilder();
        int T = Integer.parseInt(br.readLine());
        for(int i=0; i<T; i++){
            StringTokenizer st = new StringTokenizer(br.readLine(), " ");
            int X = Integer.parseInt(st.nextToken());
            int Y = Integer.parseInt(st.nextToken());
            int d = Y-X;
            int max = (int)Math.sqrt(d);
            if(max == Math.sqrt(d)){
                sb.append(max*2-1).append('\n');
            }else if(d <= max * max +max){
                sb.append(max*2).append('\n');
            }else{
                sb.append(max*2+1).append('\n');
            }
        }
        System.out.println(sb);
    }
}
```

[https://www.acmicpc.net/problem/1011](https://www.acmicpc.net/problem/1011)

[https://st-lab.tistory.com/79](https://st-lab.tistory.com/79)

