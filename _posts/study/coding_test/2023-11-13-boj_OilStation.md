---
layout: post
title:  "[백준] 13305_주유소 C++"
subtitle:   
date: 2023-11-13 06:56:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 13305_주유소 C++<br/>
<br/>

오랜만에 풀어보는 그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

typedef long long ll;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    vector<ll> Dis;
    vector<ll> Cost;

    Dis.resize(N-1);
    Cost.resize(N);

    for (int i = 0; i < N-1; ++i)
        cin >> Dis[i];
    
    for (int i = 0; i < N; ++i)
        cin >> Cost[i];
    
    ll ans = 0;
    ll CostPerOne = Cost[0];
    ll AcculDist = Dis[0];

    for (int i = 1; i < N-1; ++i)
    {
        if (CostPerOne > Cost[i])
        {
            ans += AcculDist * CostPerOne;
            CostPerOne = Cost[i];
            AcculDist = Dis[i];
        }   
        else
            AcculDist += Dis[i];
    }

    ans += CostPerOne * AcculDist;

    cout << ans;

    return 0;
}
```

코딩테스트에 자주 나오는 유형인데 오랜 시간동안 잡지 않아서 오랜만에 그리디 알고리즘 문제를 풀었다.<br/>

쬐끔 당황스러웠던 것은 보통 그리디 알고리즘 문제는 정렬을 이용하는데<br/>

이 문제는 정렬을 이용해선 안된다는 것이다.<br>

그래도 아이디어만 떠올리면 풀 수 있는 문제였다.<br/>

최대한 작은 값으로 더 긴 거리를 갈 수 있게 하면 되는 것 아닌가?<br/>

예를 들어

```
4
2 3 1
5 2 4 1
```

이라고 했을 때, 리터당 가격이 2 인 주유소에서 최대한 많이 주유하고<br/>

2보다 더 작은 값을 만날 때 까지 가면 되는 것이다.<br/>

```
10
1 2 3 4 5 6 7 8 9
8 2 3 4 5 6 7 1 9 10
```

이런 테스트 케이스가 나와도<br/>

리터 당 가격이 2인 주유소에서 최대한 많이 주유한다음<br/>

리터 당 가격이 1인 주유소에서 나머지 거리만큼 주유하면 되는 것이다.<br/>

즉, 현재 리터 당 가격을 저장하고<br/>

현재 리터 당 가격 보다 작은 가격을 만날 때 까지 거리를 계속 누적시키며<br/>

작은 가격을 만나면 ans에 누적한 값 * 리터 당 가격을 더하고<br/>

새로 갱신을 해주는 것이다.<br>