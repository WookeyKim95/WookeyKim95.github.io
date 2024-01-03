---
layout: post
title:  "[백준] 1711_직각삼각형 C++"
subtitle:   
date: 2024-01-04 00:06:40 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1711_직각삼각형 C++<br/>
<br/>

브루트포스 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

typedef long long ll;

ll m_max(ll a, ll b)
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

    int N;
    cin >> N;

    vector<pair<ll, ll>> Points;

    for (int i = 0; i < N; ++i)
    {
        int x, y;
        cin >> x >> y;
        Points.push_back({x, y});
    }

    int ans = 0;

    for (int i = 0; i < N-2; ++i)
    {
        for (int j = i+1; j < N-1; ++j)
        {
            for (int k = j+1; k < N; ++k)
            {
                pair<ll, ll> P1 = Points[i];
                pair<ll, ll> P2 = Points[j];
                pair<ll, ll> P3 = Points[k];

                ll D1 = (P1.first - P2.first) * (P1.first - P2.first) + (P1.second - P2.second) * (P1.second - P2.second);
                ll D2 = (P1.first - P3.first) * (P1.first - P3.first) + (P1.second - P3.second) * (P1.second - P3.second);
                ll D3 = (P2.first - P3.first) * (P2.first - P3.first) + (P2.second - P3.second) * (P2.second - P3.second);

                if (2 * m_max(D1, m_max(D2, D3)) == D1+D2+D3)
                    ans++;
            }
        }
    }

    cout << ans;

    return 0;
}
```

<br/>

브루트포스 알고리즘이 오래 걸리긴 하지만,<br/>

정작 브루프토스를 실행하려고 하니 3중 for문이 나오고, N의 최대값이 1500이라서<br/>

시간초과를 하지 않을까 걱정을 했다.<br/>

하지만 계산을 해보니 아슬아슬 할 것 같았다. 제한시간이 5초였다.<br/>

일단은 진행을 해보았고,<br/>

직각삼각형인지 어떻게 판단하지? 생각을 했는데 <br/>

피타고라스 정리에 따라 직각삼각형일 조건은<br/>

```
C^2 = A^2 + B^2 // 여기서 C가 제일 긴 선분.
```

이었으니, 결국에는 위 코드 상에서 D1, D2, D3에서 가장 큰 값이 D1이라고 하면<br/>

D1 = D2 + D3가 되는 것이다.<br/>

따라서, 아래와 같이 조건식을 세워주면 되는 것이다.<br/>

```
if (2 * m_max(D1, m_max(D2, D3)) == D1+D2+D3)
    ans++;
```

이렇게 해서 직각삼각형의 개수를 구할 수 있었고, 다행히 시간초과는 나지 않았다.<br/>