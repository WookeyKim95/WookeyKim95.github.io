---
layout: post
title:  "[백준] 1153_네 개의 소수 C++"
subtitle:   
date: 2023-05-29 08:11:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1153_네 개의 소수 C++<br/>
<br/>

에라토스테네스의 체와 골드바흐 추측을 이용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cmath>
#include <cstring>

#define MAX 1000000

using namespace std;

int N;
bool isPrime[MAX+1];
int PrimeNum[4];

void Search(int val)
{   
    for (int i = 2; i <= val; ++i)
    {
        if (!isPrime[i])
            continue;

        for (int j = 2; j <= val-i; ++j)
        {
            if (!isPrime[j])
                continue;
            
            if (i + j == val)
            {
                PrimeNum[2] = i;
                PrimeNum[3] = j;
                return;
            }
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    memset(isPrime, true, sizeof(isPrime));
    isPrime[1] = false;

    for (int i = 2; i <= sqrt(N); ++i)
    {
        if (!isPrime[i])
            continue;
        
        for (int j = 2*i; j <= N; j += i)
            isPrime[j] = false;
    }

    if (N < 8)
    {
        cout << -1;
        return 0;
    }
        

    if (N % 2 == 0)
    {
        PrimeNum[0] = 2;
        PrimeNum[1] = 2;
        Search(N-4);
    }
        
    else if (N % 2 != 0)
    {
        PrimeNum[0] = 2;
        PrimeNum[1] = 3;
        Search(N-5);
    }
    
    for (int i = 0; i < 4; ++i)
        cout << PrimeNum[i] << ' ';

    return 0;
}
```

<br/>

## 오답제출코드(시간초과)<br/>
<Br/>

```
#include <iostream>
#include <cmath>
#include <cstring>

#define MAX 1000000

using namespace std;

int N;
bool isPrime[MAX+1];
int PrimeNum[4];

void Search(int depth, int val)
{   
    if (depth == 4)
    {
        if (val == 0)
        {
            for (int i = 0; i < 4; ++i)
            {
                cout << PrimeNum[i] << ' ';
            }   
            exit(0);
        }
        return;
    }

    for (int i = 2; i <= val; ++i)
    {
        if (!isPrime[i])
            continue;

        PrimeNum[depth] = i;
        Search(depth+1, val-i);
        PrimeNum[depth] = 0;
    }

}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    memset(isPrime, true, sizeof(isPrime));
    isPrime[1] = false;

    for (int i = 2; i <= sqrt(N); ++i)
    {
        if (!isPrime[i])
            continue;
        
        for (int j = 2*i; j <= N; j += i)
            isPrime[j] = false;
    }

    Search(0, N);

    cout << -1;

    return 0;
}
```
<br/>

우선 소수를 판별해야 하기 때문에 에라토스테네스의 체를 먼저 구현하였다.<br/>

자, 다음 문제는 어떻게 소수 4개로 분리를 할 까에 대한 고민이다.<br/>

나는 백트래킹(혹은 DFS)를 이용하기로 하였다.<br/>

입력된 val값에서 소수를 빼주며 depth 값을 1 더해서 다음 백트래킹으로 넘어가고,<br/>

depth가 4가 되면 그 때 val값이 0인지를 판별하도록 구현하였다.<br/>

val 값이 0이라면 배열에 저장된 값을 출력하고,<br/>

그렇지 않다면 바로 다시 return 하도록 하였다.<br/>

만약에 모두 탐색했지만 불가능한 경우가 나온다면 Search 함수를 빠져나와서<br/>

-1을 출력하도록 하였다.<br/>

구현을 하면서 느꼈던 것은 중복 허용이라는 것과 스페셜 저지라는 것이 다행으로 느껴졌다.<br/>

하지만 백트래킹 특성상의 문제였을까? 시간초과가 발생하였다.<br/>
<br/>

## 정답코드로의 변환<br/>
<br/>

구글링을 해보니 나랑 똑같은 생각을 하신 분이 있었다.<br/>

[이 분의 글](https://sosoeasy.tistory.com/117)에 따르면 골드바흐의 추측으로 풀이를 하라는 것이 핵심이다.<br/>

골드바흐의 추측 : 2보다 큰 모든 짝수는 2개의 소수의 합으로 표현할 수 있다.<br/>

즉, 8이상이기만 하면 짝수는 2, 2, #, #으로<br/>

홀수는 2, 3, #, #으로 나타낼 수 있다는 것이다.<br/>

이를 이용해서 짝수이면 PrimeNum의 2자리에다가 2, 2를 넣고 나머지 2개의 소수를 찾고,<br/>

홀수이면 PrimeNum의 2자리에다가 2, 3을 넣고 나머지 2개의 소수를 찾도록 하였다.<br/>

그리고 8 미만이면 나타낼 수 없으므로 -1을 출력하도록 하였다.<br/>