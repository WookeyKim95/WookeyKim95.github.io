---
layout: post
title:  "[백준] 1016_제곱 ㄴㄴ 수 C++"
subtitle:   
date: 2023-05-22 10:21:55 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1016_제곱 ㄴㄴ 수 C++<br/>
<br/>

에라토스테네스의 체의 원리를 응용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cmath>
#include <cstring>

using namespace std;

long long Min, Max;
bool num[1000001];
int ans = 0;

int main()
{
    cin >> Min >> Max;
    memset(num, false, sizeof(num));

    for (long long i = 2; i * i <= Max; ++i)
    {
        long long n = Min / (i * i);

        if (Min % (i * i) != 0)
            n++;
        while (n * (i * i) <= Max)
        {
            num[n * (i * i) - Min] = true;
            n++;
        }
    }

    for (int i = 0; i <= Max - Min; ++i)
    {
        if (!num[i])
            ans++;
    }
    cout << ans;

    return 0;
}

```
<br/>

알고리즘 분류를 보니 에라토스테네스의 체가 있었다.<br/>

이 문제를 풀기 위해서는 에라토스테네스의 체의 작동 원리를 알 필요가 있었다.<br/>

우선, 에라토스테네스의 체는 아래와 같은 과정으로 이루어진다.<br/>

- 1을 지운다.
- 2를 제외한 2의 배수 모두 제거
- 3을 제외한 3의 배수 모두 제거
- 4는 이미 지워졌으니 5를 제외한 5의 배수 모두 제거

...

이렇게 어떤 수를 고르고 그 수를 제외한 그 수의 배수들을 모두 지워나가는 원리이다.<br/>

그런 지워나가는 과정을 응용하는 문제였다.<br/>

2부터 Max까지 수를 모두 나열한 다음에 여기서 제곱 ㄴㄴ수가 아닌 수를 지워나가는 것이다.<br/>

이렇게 하면 자연스럽게 Max까지 제곱 ㄴㄴ수가 남게되니까<br/>

Min부터 Max까지의 제곱 ㄴㄴ수를 찾아서 개수를 세면 된다.<br/>