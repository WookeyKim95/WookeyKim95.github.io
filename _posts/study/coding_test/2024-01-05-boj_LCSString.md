---
layout: post
title:  "[백준] 5582_공통 부분 문자열 C++"
subtitle:   
date: 2024-01-05 07:07:06 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 5582_공통 부분 문자열 C++<br/>
<br/>

LCS를 문자열에 적용한 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>

#define MAX 4001

using namespace std;

int DP[MAX][MAX];

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    string s1, s2;
    cin >> s1 >> s2;

    int ans = 0;

    for (size_t i = 0; i < s2.length(); ++i)
    {
        for (size_t j = 0; j < s1.length(); ++j)
        {
            if (s2[i] == s1[j])
            {
                DP[i][j] = 1;

                if (i >= 1 && j >= 1)
                    DP[i][j] += DP[i - 1][j - 1];

                ans = m_max(ans, DP[i][j]);
            }
        }
    }
    
    cout << ans;
}
```

<br/>

LCS, 즉 최장 공통 부분 수열을 문자열에 적용한 것이다.<br/>

즉, 템플릿은 결국 LCS와 같다.<br/>

s1를 문자열 1의 길이, s2를 문자열 2의 길이라고 하면<br/>

DP[0][0] 부터 DP[s2-1][s1-1] 까지 루프를 돌면서 s1과 s2가 같은 경우 1을 넣어준다.<br/>

s1[j] == s2[i]라면, 문자열이 연속되는 상태이므로 DP[i][j]에서 DP[i-1][j-1] 만큼을 더해준다.<br/>

그 후 DP의 최댓값을 갱신해준다.<br/>

DP의 최댓값이 공통부분문자열의 최대 길이가 된다.<br/>