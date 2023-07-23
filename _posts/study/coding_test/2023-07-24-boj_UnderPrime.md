---
layout: post
title:  "[백준] 1124_언더프라임 C++"
subtitle:   
date: 2023-07-24 07:39:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1124_언더프라임 C++<br/>
<br/>

에라토스테네스의 체를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cstring>
#include <vector>

using namespace std;

bool IsPrime[1000001];
int A, B;
vector<int> UnderPrimeList;

bool IsUnderPrime(int a)
{
    vector<int> PrimeList;

    while (a > 1)
    {
        if (IsPrime[a])
        {
            PrimeList.push_back(a);
            break;
        }

        for (int i = 2; i <= 1000000; ++i)
        {
            if (!IsPrime[i])
                continue;
            if (a % i == 0)
            {
                PrimeList.push_back(i);
                a /= i;
                break;
            }
        }
    }

    int Size = PrimeList.size();

    if (IsPrime[Size])
        return true;
    else
        return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    memset(IsPrime, true, sizeof(IsPrime));
    IsPrime[0] = false;
    IsPrime[1] = false;

    cin >> A >> B;

    for (int i = 2; i <= 1000000; ++i)
    {
        for (int j = 2*i; j <= B; j += i)
            IsPrime[j] = false;
    }

    int ans = 0;
    for (int i = A; i <= B; ++i)
    {
        if (IsUnderPrime(i))
            ans++;
    }

    cout << ans;

    return 0;
}
```


<br/>

## 오답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cstring>
#include <vector>

using namespace std;

bool IsPrime[1000001];
int A, B;
vector<int> UnderPrimeList;

bool IsUnderPrime(int a)
{
    vector<int> PrimeList;

    while (a > 1)
    {
        if (IsPrime[a])
        {
            PrimeList.push_back(a);
            break;
        }

        for (int i = 2; i <= 1000000; ++i)
        {
            if (!IsPrime[i])
                continue;
            if (a % i == 0)
            {
                PrimeList.push_back(i);
                a /= i;
            }
        }
    }

    int Size = PrimeList.size();

    if (IsPrime[Size])
        return true;
    else
        return false;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    memset(IsPrime, true, sizeof(IsPrime));
    IsPrime[0] = false;
    IsPrime[1] = false;

    cin >> A >> B;

    for (int i = 2; i <= 1000; ++i)
    {
        for (int j = 2*i; j <= i*i; j += i)
            IsPrime[j] = false;
    }

    int ans = 0;
    for (int i = A; i <= B; ++i)
    {
        if (IsUnderPrime(i))
            ans++;
    }

    cout << ans;

    return 0;
}
```

에라토스테네스의 체를 이용해서 소수를 판별하고,<br/>

소인수 분해를 하고 그렇게 나온 소인수들의 개수를 구하도록 구현하였다.<br/>

일단은 제한시간은 2초고, 이렇게 짜니까 시간이 오래걸리지 않을까 걱정하였다.<br/>

그런데 시간은 그렇게 오래걸리지는 않나보다.<br/>

퍼센티지가 올라가다가 틀렸습니다 라는 문구가 출력되었다.<br/>

원인을 찾아보니 에라토스테네스의 체를 잘못 설계한 것 같았다.<br/>

```
for (int i = 2; i <= 1000; ++i)
{
    for (int j = 2*i; j <= i*i; j += i)
        IsPrime[j] = false;
}
```
아! 이렇게 하면 1000을 넘어가는 부분에서는 소수판별이 이루어지지 않으니까<br/>

잘못 나오나보다.<br/>

```
for (int i = 2; i <= 1000000; ++i)
{
    for (int j = 2*i; j <= B; j += i)
        IsPrime[j] = false;
}
```
범위 내에 있는 모든 수에 대해서 판별할 수 있도록<br/>

위와 같이 고쳐주니 정답처리가 되었다!<br/>

우려와는 달리 시간초과는 나지 않았다.<br/>