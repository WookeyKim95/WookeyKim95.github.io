---
layout: post
title:  "[백준] 5557_1학년 C++"
subtitle:   
date: 2024-01-06 07:37:27 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 5557_1학년 C++<br/>
<br/>

백트래킹이 아니라 DP였던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

#define MAX 101

typedef long long ll;

int N;
int Arr[MAX];
ll DP[MAX][21];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
        cin >> Arr[i];
    
    DP[0][Arr[0]] = 1;

    for (int i = 1; i < N-1; ++i)
    {
        for (int j = 0; j < 21; ++j)
        {
            if (DP[i-1][j])
            {
                if (j+Arr[i] <= 20)
                    DP[i][j + Arr[i]] += DP[i-1][j];

                if (j-Arr[i] >= 0)
                    DP[i][j - Arr[i]] += DP[i-1][j];  
            }
        }
    }

    cout << DP[N-2][Arr[N-1]];

    return 0;
}
```

<br/>

DP[i][j]에는 i번째 등식까지의 합이 j인 경우의 개수가 담겨있다.<br/>

i+1번째를 추가할때 j+Arr[i+1]과 j-Arr[i+1]이 0~20사이의 값이라면<Br/>

DP[i+1][j+Arr[i+1]]과 DP[i+1][j-Arr[i+1]]은 각각 기존의 값에 DP[i][j]를 더한 값을 가지게 된다.<br/>

N-2번째까지의 등식합이 N-1번째 숫자가 되야하므로 DP[N-2][Arr[N-1]]을 출력하면 된다.