---
layout: post
title:  "[백준] 1007_벡터 매칭 C++"
subtitle:   
date: 2023-05-05 12:07:25 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1007_벡터 매칭 C++<br/>
<br/>

브루트포스 알고리즘을 사용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cmath>
#include <vector>
#include <cstring>

#define INF 1000000000

using namespace std;

int TC;
vector<pair<double, double>> P;
double result;
bool visited[21];
int N;

double m_min(double a, double b)
{
    if (a < b)
        return a;
    else
        return b;
}

double CalLength(double a, double b)
{
    double ret = sqrt(a*a + b*b);
    return ret;
}

double VecSum()
{
    pair<double, double> point = {0.f, 0.f};

    for (size_t i = 0; i < P.size(); ++i)
    {
        if (visited[i])
        {
            point.first -= P[i].first;
            point.second -= P[i].second;
        }

        else
        {
            point.first += P[i].first;
            point.second += P[i].second;
        }
    }

    return CalLength(point.first, point.second);
}

void backtrack(int now, int cnt)
{
    if (cnt == N/2)
    {
        result = m_min(result, VecSum());
        return;
    }

    for (int i = now; i < N; ++i)
    {
        visited[i] = true;
        backtrack(i+1, cnt+1);
        visited[i] = false;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> TC;

    for (int i = 0; i < TC; ++i)
    {
        result = INF;
        memset(visited, false, sizeof(visited));
        P.clear();
        
        cin >> N;

        for (int j = 0; j < N; ++j)
        {
            double _input1, _input2;
            cin >> _input1 >> _input2;

            P.push_back(make_pair(_input1, _input2));
        }

        backtrack(0, 0);

        cout << fixed;
        cout.precision(8);
        cout << result << '\n';
    }

    return 0;
}
```

완전 탐색을 해야한다는 것은 알고 있었는데, 오랜만이라 그런지 백트래킹을 이용해서 완전탐색할 생각을 못했다.<br/>

백트래킹을 해서 완전탐색 알고리즘을 구현하였다.<br/>

방문 상태인 점을 시작점으로 표현하고, 그렇지 않은 점은 끝점으로 표현한다.<br/>

이렇게 한다음에, (끝점의 좌표의 합) - (시작 점의 좌표의 합)을 구하면 총 벡터의 합이 나온다.<br/>

```
double VecSum()
{
    pair<double, double> point = {0.f, 0.f};

    for (size_t i = 0; i < P.size(); ++i)
    {
        if (visited[i])
        {
            // 시작점일 경우
            point.first -= P[i].first;
            point.second -= P[i].second;
        }

        else
        {
            // 끝점, 도착점일 경우
            point.first += P[i].first;
            point.second += P[i].second;
        }
    }

    return CalLength(point.first, point.second);
}


```

그 합의 길이를 일일이 구한다음에 최솟값을 구하는 형식으로 구현하였다.<br/>

