---
layout: post
title:  "[백준] 1027번_고층 건물 C++"
subtitle:   
date: 2023-04-01 09:19:19 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1027번_고층 건물 C++<br/>
<br/>

기하학과 완전탐색을 같이 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

vector<int> buildings;
int N;

bool Check(int a, int b, int c)
{
    // 분자와 분모
    double numer = (double)(buildings[a] - buildings[b]);
    double deno = (double)(a - b);

    double y = ((numer/deno)*(double)c) + (double)buildings[b] - ((double)b*(numer/deno));

    // 교차할 경우 false 반환
    if (y <= (double)buildings[c])
        return false;
    else
        return true;
}

int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int building;
        cin >> building;

        buildings.push_back(building);
    }

    // N = 1일 경우 예외처리
    if (N == 1)
    {
        cout << 0;
        return 0;
    }
    // N = 2일 경우 예외처리
    else if (N == 2)
    {
        cout << 1;
        return 0;
    }

    int ans = 0;
    
    for (int i = 0; i < buildings.size(); ++i)
    {
        int CanSee = 0;

        // j < i인 구간
        for (int j = 0; j < i; ++j)
        {
            bool flag = true;
            for (int k = j+1; k < i; ++k)
            {
                if (!Check(j, i, k))
                {
                    flag = false;
                    break;
                }
            }
            if (flag)
                CanSee++;
        }

        // j > i인 구간
        for (size_t j = i + 1; j < buildings.size(); ++j)
        {
            bool flag = true;
            for (size_t k = i+1; k < j; ++k)
            {
                if (!Check(i, j, k))
                {
                    flag = false;
                    break;
                }
                    
            }
            if (flag)
                CanSee++;
        }
        if (ans < CanSee)
            ans = CanSee;
    }

    cout << ans;

    return 0;
}
```

우선, A와 B 빌딩의 꼭대기를 잇는 선분이 다른 빌딩과 만나는가 여부는 1차방정식을 사용하였다.<br/>

```
bool Check(int a, int b, int c)
{
    // 분자와 분모
    double numer = (double)(buildings[a] - buildings[b]);
    double deno = (double)(a - b);

    double y = ((numer/deno)*(double)c) + buildings[b] - ((double)b*(numer/deno));

    // 교차할 경우 false 반환
    if (y <= buildings[c])
        return false;
    else
        return true;
}
```

좌표 (ax, ay), (bx, by)를 잇는 직선의 1차방정식은 아래와 같다.<br/>
<br/>

$y=\frac{by-ay}{bx-ax}x + by - \frac{by-ay}{bx-ax}bx$

<br/>
따라서 위에 있는 코드와 같이 y를 구하는 식을 구현하였다.<br/>

그리고 y값보다 a와 b 사이에 있는 빌딩 c의 높이가 높다면 false를, 낮다면 true를 반환하도록 하였다.<br/>

```
// N = 1일 경우 예외처리
    if (N == 1)
    {
        cout << 0;
        return 0;
    }

    int ans = 0;
    
    for (int i = 0; i < buildings.size(); ++i)
    {
        // 마주보는 빌딩은 무조건 볼 수 있으므로 1
        int CanSee = 1;

        // j < i인 구간
        for (int j = 0; j < i; ++j)
        {
            bool flag = true;
            for (int k = j+1; k < i; ++k)
            {
                if (!Check(j, i, k))
                    flag = false;
            }
            if (flag)
                CanSee++;
        }

        // j > i인 구간
        for (size_t j = i + 1; j < buildings.size(); ++j)
        {
            bool flag = true;
            for (size_t k = i+1; k < j; ++k)
            {
                if (!Check(i, j, k))
                    flag = false;
            }
            if (flag)
                CanSee++;
        }
        if (ans < CanSee)
            ans = CanSee;
    }
```

탐색 구간은 위와 같이 삼중 for문으로 구현하였다.<br/>

빌딩 a와 빌딩 b는 맨 밖에 있는 2중 for문으로 구하고, i와 j 사이에 있는 k로 빌딩 c를 고르도록 하였다.<br/>

그리고 j < i일 때와 j > i일 때를 구분하여 탐색하도록 구현하였다.<br/>

Check가 false라는 것은 중간에 막힌다는 것으로 i빌딩에서 j빌딩을 볼수 없기 때문에 flag = false로 돌리고 break하도록 구현하였다.<br/>