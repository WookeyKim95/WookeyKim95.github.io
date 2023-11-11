---
layout: post
title:  "[백준] 11055_가장 큰 증가하는 부분 수열 C++"
subtitle:   
date: 2023-11-12 07:05:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11055_가장 큰 증가하는 부분 수열 C++<br/>
<br/>

가장 큰 증가하는 부분 수열에 대해서 풀어보는 DP 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MAX 1001

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int Arr[MAX];
    int DP[MAX];

    int N;
    cin >> N;

    for (int i = 1; i <= N; i++)
        cin >> Arr[i];
    
    int Max = 0;
    for (int i = 1; i <= N; ++i)
    {
        DP[i] = Arr[i];
        for (int j = 1; j < i; ++j)
        {
            if (Arr[j] < Arr[i] && DP[i] < DP[j] + Arr[i])
                DP[i] = DP[j] + Arr[i];
        }
        if (Max < DP[i])
            Max = DP[i];
    }
    cout << Max;

    return 0;
}

```

LCS(가장 긴 증가하는 부분 수열)를 응용한 대표적인 DP 유형 알고리즘 문제일 것이다.<br/>

LCS 템플릿은 보통 아래와 같다.<br/>

[출처](https://yabmoons.tistory.com/516)<br/>

```
int Answer = 0;
for (int i = 1; i <= N; i++)
{
    DP[i] = 1;
    for (int j = i - 1; j >= 1; j--)
    {
        if (Arr[i] > Arr[j])
        {
            DP[i] = Max(DP[i], DP[j] + 1);
        }
    }
    Answer = Max(DP[i], Answer);
}
```

여기서 DP에다가 무엇을 저장해줄지 값을 변경을 해주고 조건을 바꿔주면 되는 것이다.<br/>

그리고 이 문제 같은 경우는 j 구간을 오른쪽이 아닌 왼쪽부터 증가 시켰다.<br/>

그렇게 해야 Max값을 올바르게 갱신할 수 있으니까.<br/>

그래서 이 부분이 아래와 같이 바뀌면 되는 것이다.<br/>

```
int Max = 0;
for (int i = 1; i <= N; ++i)
{
    DP[i] = Arr[i];
    for (int j = 1; j < i; ++j)
    {
        if (Arr[j] < Arr[i] && DP[i] < DP[j] + Arr[i])
            DP[i] = DP[j] + Arr[i];
    }
    if (Max < DP[i])
        Max = DP[i];
}
```

하나 더 메모해두면, LCS를 구하는 DP 알고리즘의 시간 복잡도는 O(N^2)이다.<br/>

시험에 나왔길래 메모해둬야지.