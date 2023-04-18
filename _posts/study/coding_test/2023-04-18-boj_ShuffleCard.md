---
layout: post
title:  "[백준] 1091번_카드 섞기 C++"
subtitle:   
date: 2023-04-18 10:07:43 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1091번_카드 섞기 C++<br/>
<br/>

좀 머리아픈 구현 문제.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>
#include <set>

using namespace std;

int N;
vector<int> P, S, V1, V2;
set<vector<int>> visited;

void Func(vector<int>& a, vector<int>& b)
{
    for (size_t i = 0; i < a.size(); ++i)
        b[S[i]] = a[i];
    
    return;
}

int VectorCheck(vector<int>& v)
{
    int flag = 1;
    for (int i = 0; i < N; ++i)
    {
        if (i % 3 != P[v[i]])
        {
            flag = 0;
            break;
        }
    }

    if (visited.find(v) == visited.end())
        visited.insert(v);
    else
        flag = 2;
    
    return flag;
}


int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        P.push_back(_input);
    }

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        S.push_back(_input);
    }

    for (int i = 0; i < N; ++i)
        V1.push_back(i);
    
    for (int i = 0; i < N; ++i)
        V2.push_back(0);

    int ans = 0;
    bool flag = false;

    while (true)
    {
        int tmp = 0;
        if (!flag)
        {
            tmp = VectorCheck(V1);
            Func(V1, V2);
        }
        else
        {
            tmp = VectorCheck(V2);
            Func(V2, V1);
        }

        if (tmp >= 1)
        {
            if (tmp == 2)
                ans = -1;
            break;
        }

        flag = !flag;
        ans++;
    }

    cout << ans;

    return 0;
}
```

간단해 보이지만 다소 복잡한 문제였던 것 같다.<br/>

카드의 배열을 벡터로 놓았고, 카드를 섞을 때마다 벡터를 어떻게 바꿀지 고민했었다.<br/>

이때는 벡터를 2개 이용하기로 했다.<br/>

벡터 2개를 이용하며 서로 번갈아가면서 카드를 받는 형식으로 구현하였다.<br/>

어느 벡터에 카드 배치를 넣을 지는 while문 안에 있는 flag변수를 이용하였다.<br/>

그리고, tmp 값이 상태를 받는다.<br/>

카드가 원하는 상태로 배치되었을 경우 1을 반환하고, 그렇지 않을 경우 0을 반환하며,<br/>

만약에 이미 섞은 적이 있는 카드가 나온거라면 2를 반환하게 된다.<br/>

그리고 2를 반환했을 경우 즉시 ans = -1 처리를 한다.<br/>

이 과정에서는 set을 사용하였다.<br/>