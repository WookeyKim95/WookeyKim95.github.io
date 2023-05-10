---
layout: post
title:  "[백준] 1744_수 묶기 C++"
subtitle:   
date: 2023-05-10 09:29:34 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1744_수 묶기 C++<br/>
<br/>

정렬과 그리디 알고리즘을 사용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int N, ans = 0;
vector<int> vp, vn;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int VecSum(vector<int> v)
{
    int ret = 0;
    int sum = 0;

    int len = v.size();

    if (len == 1)
        return v[0];
    
    for (int i = 0; i < len; ++i)
        sum += v[i];

    if (len % 2 == 0)
    {
        for (int i = 0; i < len; i += 2)
        {
            if (v[i] + v[i+1] > v[i] * v[i+1])
                ret += v[i] + v[i+1];
            else
                ret += v[i] * v[i+1];
        }
    }

    else
    {
        int ret1 = 0;
        for (int i = 0; i < len-1; i += 2)
        {
            if (v[i] + v[i+1] > v[i] * v[i+1])
                ret1 += v[i] + v[i+1];
            else
                ret1 += v[i] * v[i+1];
        }
        ret1 += v[len-1];

        int ret2 = v[0];
        for (int i = 1; i < len; i += 2)
        {
            if (v[i] + v[i+1] > v[i] * v[i+1])
                ret2 += v[i] + v[i+1];
            else
                ret2 += v[i] * v[i+1];
        }

        ret = m_max(ret1, ret2);
    }

    ret = m_max(ret, sum);

    return ret;
}

int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        if (_input > 0)
            vp.push_back(_input);
        else
            vn.push_back(_input);
    }

    if (N == 1)
    {
        if (vn.size() == 1 && vp.size() == 0)
            cout << vn[0];
        else if (vn.size() == 0 && vp.size() == 1)
            cout << vp[0];
        
        return 0;
    }

    sort(vp.begin(), vp.end());
    sort(vn.begin(), vn.end());

    ans += VecSum(vp);
    ans += VecSum(vn);

    cout << ans;

    return 0;
}
```

<br/>

우선, 입력 받은 수열을 먼저 정렬을 해준다.<br/>

그리고 음수는 음수끼리, 양수는 양수끼리 묶는 것이 유리하기 때문에<br/>

음수와 양수 따로 벡터를 만들어서 수열을 받는 것으로 구현하였다.<br/>

그리고 수열을 더하는 과정은 경우의 수를 분리하였다.<br/>

- 묶지 않고 더하기만 하는 경우
- 개수가 홀수일 경우 1 : 맨 마지막만 빼고 모두 묶는 경우
- 개수가 홀수일 경우 2 : 맨 앞만 빼고 모두 묶는 경우
- 개수가 짝수일 경우 : 모두 묶는 경우

사실, 정렬을 한 이상, 수열의 구성요소가 정수이기 때문에 무조건 되도록이면 많이 묶는 것이 유리하다.<br/>

그렇기 때문에 어떻게 묶을지에 대해서 고민을하고 경우의 수를 분리해서 구현하였다.<br/>