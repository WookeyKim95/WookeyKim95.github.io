---
layout: post
title:  "[백준] 1102_발전소 C++"
subtitle:   
date: 2023-06-04 10:04:52 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1102_발전소 C++<br/>
<br/>

비트마스킹, DP를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>

#define MAXGEN 16
#define INF 99999999

using namespace std;

struct Gener
{
    int num;
    vector<pair<int, int>> link;

    Gener(int _num) : num(_num) {};
};

int N, P, ans = INF;
int Cost[1 << MAXGEN];
vector<Gener> Generators;

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int BitCount(int a)
{
    int cnt = 0;
    while (a != 0)
    {
        cnt = cnt + (a & 1);
        a = a >> 1;
    }
    return cnt;
}

int DFS(int State)
{
    if (BitCount(State) >= P)
        return 0;
    
    if (Cost[State] != -1)
        return Cost[State];
    
    Cost[State] = INF;
    
    for (int i = 1; i <= N; ++i)
    {
        if ((State & (1 << (i-1))) == 0)
            continue;
        
        for (int j = 0; j < N; ++j)
        {
            if ((State & (1 << j)) == 0)
            {
                int NextState = State | (1 << j);
                Cost[State] = m_min(Cost[State], Generators[i].link[j].second + DFS(NextState));
            }
        }
    }

    return Cost[State];
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    cin >> N;
    
    Gener padding(0);
    Generators.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        Gener G(i);
        Generators.push_back(G);
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            int _input;
            cin >> _input;
            
            Generators[i].link.push_back({j, _input});
        }
    }

    int StateBit = 0;
    for (int i = 0; i < N; ++i)
    {
        char c;
        cin >> c;

        if (c == 'Y')
            StateBit = StateBit | (1 << i);
    }

    cin >> P;

    memset(Cost, -1, sizeof(Cost));

    ans = DFS(StateBit);

    if (ans == INF)
        cout << -1;
    else
        cout << ans;

    return 0;
}
```
<br/>

[풀이 참고 링크](https://yabmoons.tistory.com/359)<br/>

DFS, 그래프 문제인줄 알았더니 비트마스킹 + DP라서 놀랬다.<br/>

각 발전소 별로 상태를 비트로 나타내기가 쉽기 때문에 비트마스킹을 이용하면 되는 문제였다.<br/>

발전소가 켜져있으면 1로, 꺼져있으면 0으로 비트를 나타내고 계산을 해주도록 했다.<br/>

그리고 각 상태별 COST를 저장해줄 배열을 선언해서 DFS를 통해 DP를 구현하였다.<br/>

켜져있는 비트를 세는 방법은 아래와 같이 구현되었다.<br/>

```
int BitCount(int a)
{
    int cnt = 0;
    while (a != 0)
    {
        cnt = cnt + (a & 1);
        a = a >> 1;
    }
    return cnt;
}
```

a라는 비트의 가장 오른쪽이 켜져있는지 꺼져있는지를 판별하는 방법은 1과 비트연산을 시키는 것이다.<br/>

AND연산을 시켜서 1이 나온다면 켜져있다는 것이고, 0이 나오면 꺼져있다는 것으로 손쉽게 판별할 수 있다.<br/>

```
for (int i = 1; i <= N; ++i)
{
    if ((State & (1 << (i-1))) == 0)
        continue;
    
    for (int j = 0; j < N; ++j)
    {
        if ((State & (1 << j)) == 0)
        {
            int NextState = State | (1 << j);
            Cost[State] = m_min(Cost[State], Generators[i].link[j].second + DFS(NextState));
        }
    }
}
```

vector에 저장된 발전소 인덱스 때문에 for문이 1부터 N까지 돌도록 하고, 비트 연산에서 i-1을 붙였다.<br/>

만약에 i번째 발전소가 꺼져있는 상태라면 continue를 하도록 하고,<br/>

켜져있는 상태라면 다음 발전소를 키고 그 값을 계산하도록 하였다.<br/>

