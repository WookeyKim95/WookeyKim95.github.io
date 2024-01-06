---
layout: post
title:  "[백준] 3079_입국심사 C++"
subtitle:   
date: 2024-01-07 07:30:37 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 3079_입국심사 C++<br/>
<br/>

매개변수탐색 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>

using namespace std;

typedef long long ll;

int main()
{
    ll n, m;
    ll t[100001];

    cin >> n >> m;
    
    for (int i = 0; i < n; ++i)
        cin >> t[i];

    sort(t, t + n);

    ll high = m * t[0];
    ll low = 1;
    ll mid;
    ll ans = 0;
    ll people;

    while (high >= low)
    {
        people = 0;
        mid = (high + low) / 2;

        for (int i = 0; i < n; ++i)
            people += mid / t[i];

        if (people >= m)
        {
            if (ans > mid || ans == 0)
                ans = mid;

            high = mid - 1;
        }

        else
            low = mid + 1;
    }
    
    cout << ans;

    return 0;
}
```

<br/>

이분탐색, 매개변수탐색을 이용하는 문제였다.<br/>

시간을 기준으로 해서 각 시간마다 얼만큼의 사람을 통과시킬 수 있는지로 구현을 하여서<br/>

심사할 수 있는 인원의 조건을 만족하는 시간 중에서 최소값을 찾아주는 식으로 구현하였다.<br/>

