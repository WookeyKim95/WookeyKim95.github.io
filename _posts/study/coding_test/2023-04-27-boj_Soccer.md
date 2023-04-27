---
layout: post
title:  "[백준] 1344_축구 C++"
subtitle:   
date: 2023-04-27 09:14:03 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1344_축구 C++<br/>
<br/>

조합론, 확률론을 이용하는 dp문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <cmath>
#include <cstring>

using namespace std;

bool num[19];
double a, b, dp[19][19][19];

double counter(int x, int y, int depth)
{
    if (depth == 18)
    {
        if(num[x] || num[y])
            return 1.f;
        else
            return 0.f;
    }

    double &ret = dp[x][y][depth];

    if (ret >= -1.f)
        return ret;
    
    ret = 0.f;
    ret += counter(x, y, depth + 1) * (1.f - a) * (1.f - b);
    ret += counter(x, y + 1, depth + 1) * (1.f - a) * b;
    ret += counter(x + 1, y, depth + 1) * a * (1.f - b);
    ret += counter(x + 1, y + 1, depth + 1) * a * b;

    return ret;
}

int main()
{
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    fill(num, num + 20, 1);
    memset(dp, -1, sizeof(dp));

    num[0] = 0.f;
    num[1] = 0.f;

    // 소수 구하기(에라토스테네스의 체 알고리즘)
    for (int i = 2; i <= 19; i++)
    {
        for (int j = i + i; j <= 19; j += i)
        {
            num[j] = 0;
        }
    }

    cin >> a;
    cin >> b;
    
    a /= 100.f;
    b /= 100.f;
    
    cout << fixed;
    cout.precision(10);
    cout << counter(0, 0, 0);
}
```

팀 별로 한 골만 넣은 경우, 두 골만 넣은 경우, ... , 18골을 모두 넣은 경우를<br/>

따질 필요가 있었다.<br/>

이번 문제는 18이라는 한도가 정해져있었기 때문에 재귀용법을 사용할 수 있었다.<br/>

다만, 배열을 어떻게 저장해야할지 감이 안와서 많이 해맸던 것 같다.<br/>

고심한 끝에 구글링을 해서 [참고링크](https://velog.io/@kddkdkk/%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%EB%B0%B1%EC%A4%80-1344-%EC%B6%95%EA%B5%AC)를 참고하기로 하였다.<br/>

여기서는 재귀용법을 쓰고 dp용법을 3차원으로 저장하였다.<br/>

- 두 팀 다 골을 못넣은 경우
- A 팀만 골을 넣은 경우
- B 팀만 골을 넣은 경우
- 두 팀 다 골을 넣은 경우

네 가지를 치고, 재귀함수를 돌리면서 확률을 누적시켜서 구하는 것이었다.<br/>

그리고, 원하는 확률은 소수일 경우만 이므로,<br/>

에라토스테네스의 체를 이용해 소수를 구해서 그 확률만 구하도록 하였다.<br/>