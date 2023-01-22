---
layout: post
title:  "[프로그래머스] 예상 대진표 C++"
subtitle:   
date: 2023-01-21 14:20:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 예상 대진표 C++<br/>
<br/>

2의 지수를 이용하여 푸는 문제
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int solution(int n, int a, int b)
{
    int answer = 1;
    
    while (!((a-b == 1 && a%2== 0) || (b-a == 1 && b%2 == 0)))
    {
        answer++;
        
        if (a % 2 == 1)
            a += 1;
        if (b % 2 == 1)
            b += 1;

        if (a > 1)
            a /= 2;
        if (b > 1)
            b /= 2;
    }

    return answer;
}
```

대진표를 예상해보자.

7번 선수에서 시작했다고 가정하면 7 - 4 - 2 순서대로 흘러갈 것이고,<br/>

3번 선수에서 시작했다고 가정하면 3 - 1,<br/>

14번에서 시작했다고 가정하면 14 - 7 - 4 - 2 - 1로 흘러갈 것이다.<br/>

그래서 홀수면 1을 더해주고 짝수면 그냥 2를 나눠버리는 방법을 택했다.<br/>

그리고 중지 조건은 while문 조건에 있는 대로 a와 b의 차이가 1이면서 a가 b보다 더 큰 짝수일 때

혹은 a와 b의 차이가 1이면서 b가 a보다 더 큰 짝수일 때 중지하도록 구현하였다.<br/>

예외 처리 사항을 보자.
- 차이가 1일 경우만 생각하면 만약에 a = 6, b = 7인 경우가 발생해버리면 오답이 출력될 수 있다.

그래서 조건문을 저렇게 만들었다.
