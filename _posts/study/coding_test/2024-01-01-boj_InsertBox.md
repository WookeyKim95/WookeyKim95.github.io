---
layout: post
title:  "[백준] 1965_상자넣기 C++"
subtitle:   
date: 2024-01-01 03:03:11 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1965_상자넣기 C++<br/>
<br/>

DP 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

#define MAX 1001

using namespace std;

int N;
int Arr[MAX];
int DP[MAX];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
        cin >> Arr[i];
    
    int Max = 0;
    for (int i = 1; i <= N; ++i)
    {
        DP[i] = 1;
        for (int j = 1; j < i; ++j)
        {
            if (Arr[j] < Arr[i] && DP[i] < DP[j] + 1)
                DP[i] = DP[j] + 1;
        }

        if (Max < DP[i])
            Max = DP[i];
    }

    cout << Max;
    
    return 0;
}
```

결국에는 이 문제는 해석하면<br/>

가장 긴 증가하는 부분 수열을 구하는 문제이다.<br/>

먼저, 초깃값으로 DP[i] = 1을 지정해주어야 한다.<br/>

자기자신을 선택하는 순간 DP 값은 1이 되니까.<br/>

그리고 DP는 넣을 수 있는 상자의 개수를 가리킨다.<br/>

```
if (Arr[j] < Arr[i] && DP[i] < DP[j] + 1)
    DP[i] = DP[j] + 1;
```

즉, i의 크기가 j보다 크면서 더 많은 상자를 넣을 수 있을 경우<br/>

DP[i]의 값을 새로 갱신해주는 것이다.<br/>

이 조건을 따라 코드를 작동시키면 값을 구할 수 있다.<br/>

