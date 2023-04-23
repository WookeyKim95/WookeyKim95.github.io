---
layout: post
title:  "[백준] 1806번_부분합 C++"
subtitle:   
date: 2023-04-23 09:21:03 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1647번_부분합 C++<br/>
<br/>

투 포인터를 사용하는 기초 부분합 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int main()
{
    int N, S;
    cin >> N >> S;

    vector<int> v;

    int sum = 0;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        v.push_back(_input);
        sum += _input;
    }

    if (sum < S)
    {
        cout << 0;
        return 0;
    }

    sum = 0;

    int P1 = 0, P2 = 0, ans = N;
    while (P1 <= N && P2 <= P1)
    {
        if (sum < S)
        {
            sum += v[P1];
            P1++;
        }
        else
        {
            ans = m_min(P1 - P2, ans);
            sum -= v[P2];
            P2++;
        }
    }

    cout << ans;

    return 0;
}
```

투 포인터를 사용하는 문제였다.<br/>

포인터 두개를 먼저 0에다가 배치한 뒤, sum이 S보다 작을경우<br/>

sum에다가 P1이 가리키고 있는 수를 더한 뒤 P1을 한칸 오른쪽으로 보내고,<br/>

합이 크거나 같을 경우 정답을 갱신한 뒤 P2가 가리키고있는 있는 수를 빼고<br/>

P2를 한칸 오른쪽으로 보내는 식으로 구현하였다.<br/>

이렇게 하면서 자동으로 연속하는 수열 중 가장 작은 길이를 가지고 있는 수열을<br/>

발견할 수 있다.<br/>