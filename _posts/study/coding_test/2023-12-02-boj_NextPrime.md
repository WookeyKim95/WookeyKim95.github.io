---
layout: post
title:  "[백준] 4134_다음 소수 C++"
subtitle:   
date: 2023-12-02 06:43:33 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 4134_다음 소수 C++<br/>
<br/>

소수 판별을 해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

unsigned int N;
int T;

bool isPrime(unsigned int num) 
{
    if (num <= 1) 
        return false;

    if (num == 2 || num == 3) 
        return true;

    if (num % 2 == 0 || num % 3 == 0)
        return false;

    for (unsigned int i = 5; i * i <= num; ++i) 
    {
        if (num % i == 0 || num % (i + 2) == 0) 
            return false;
    }

    return true;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> T;

    for (int tc = 0; tc < T; ++tc)
    {
        unsigned int n;
        cin >> n;
        while (!isPrime(n))
            n++;
        cout << n << '\n';
    }
}
```

오랜만에 소수에 관한 문제를 풀었다.<br/>

오랜만에리서 그런지 기억이 가물가물해서 실버 문제임에도 불구하고 풀기가 힘들었다.<br/>

일단은 주어진 시간은 1초이고, 범위는 40억 까지였기 때문에 빠르게 소수를 판별하는 것이 관건이었다.<br/>

그냥 입력된 값 부터 시작해서 소수인지 아닌지 판별하는 시스템은 구축했다.<br/>

문제는 어떻게 소수를 판별하냐 였는데<br/>

- 2와 3을 제외한 수가 2나 3으로 나누어 떨어지면 소수가 아니다.
- i가 5부터 시작해서 i나 i+2로 나누어 떨어지면 소수가 아니다.
- 탐색을 빠르게 하기 위해서 i, i+2로 선정하였다.

이렇게 판별을 할 수 있었다.<br/>

이렇게 해서 1초안에 완전탐색을 완성시킬 수 있었다.<br/>