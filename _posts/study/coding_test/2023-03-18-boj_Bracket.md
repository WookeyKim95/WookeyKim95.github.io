---
layout: post
title:  "[백준] 16637번_괄호 추가하기 C++"
subtitle:   
date: 2023-03-18 10:25:01 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 16637번_괄호 추가하기 C++<br/>
<br/>

백트래킹을 이용해서 풀었던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <stdlib.h>

using namespace std;

int N, answer = -999999999;
string s;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int m_Operator(int a, int b, char o)
{
    if (o == '+')
        return a+b;
    else if (o == '*')
        return a*b;
    else
        return a-b;
}

void backtrack(int value, int index)
{
    if (index == N-1)
    {
        answer = m_max(value, answer);
        return;
    }

    if (index < N-2)
        backtrack(m_Operator(value, atoi(&s[index+2]), s[index+1]), index+2);

    if (index < N-4)
        backtrack(m_Operator(value, m_Operator(atoi(&s[index+2]), atoi(&s[index+4]), s[index+3]), s[index+1]), index+4);
}

int main()
{
    cin >> N;
    cin >> s;

    backtrack(atoi(&s[0]), 0);
    
    cout << answer;
    
    return 0;
}
```
<br/>

아직 내 실력으로는 혼자서 풀지 못할 것 같아서 고민하다가 구글링을 했다.<br/>

[참고링크](https://velog.io/@weenybeenymini/%EB%B0%B1%EC%A4%80-16637%EB%B2%88-%EA%B4%84%ED%98%B8-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0)와 문제에 따르면,<br/>

괄호 밖에 또 괄호를 묶지 못하고, 괄호 안에 연산자는 하나만 들어갈 수 있으므로<br/>

string으로 입력 받아서 index를 옮기면서 값을 들고다니며 계산을 해주고, 그 결과가 가장 큰 값을 입력해주면 되는 것이다.<br/>

그래서 백트래킹 함수에서

```
    if (index < N-2)
        backtrack(m_Operator(value, atoi(&s[index+2]), s[index+1]), index+2);

    if (index < N-4)
        backtrack(m_Operator(value, m_Operator(atoi(&s[index+2]), atoi(&s[index+4]), s[index+3]), s[index+1]), index+4);
```

else if가 붙어있지 않다.<br/>

(A+B)+(C+D)를 계산해주었다면 그 다음으로<br/>

A+(B+C)+D를 계산해주는 경우도 고려해야하니까.<br/>

이렇게 모든 string을 탐색하고 계산할 때 까지 백트래킹을 구동하면 최대 값을 구할 수 있다.<br/>