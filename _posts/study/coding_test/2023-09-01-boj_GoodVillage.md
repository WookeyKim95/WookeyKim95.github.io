---
layout: post
title:  "[백준] 1949_우수 마을 C++"
subtitle:   
date: 2023-09-01 07:03:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1949_우수 마을 C++<br/>
<br/>

트리에서의 다이나믹 프로그래밍을 학습하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

struct Vill
{
    int DP[2];
    vector<int> link;
    int Point;
    bool visited = false;
};

int N;
vector<Vill> Vills;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void dfs(int cur)
{
    Vills[cur].visited = true;

    Vills[cur].DP[0] = 0;
    Vills[cur].DP[1] = Vills[cur].Point;

    for (size_t i = 0; i < Vills[cur].link.size(); ++i)
    {
        int Next = Vills[cur].link[i];
        
        if (Vills[Next].visited)
            continue;
        
        dfs(Next);
        Vills[cur].DP[0] += m_max(Vills[Next].DP[0], Vills[Next].DP[1]);
        Vills[cur].DP[1] += Vills[Next].DP[0];
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    Vill Dummy;
    Vills.push_back(Dummy);

    for (int i = 1; i <= N; ++i)
    {
        Vill v;
        cin >> v.Point;
        Vills.push_back(v);
    }

    for (int i = 0; i < N-1; ++i)
    {
        int s, e;
        cin >> s >> e;

        Vills[s].link.push_back(e);
        Vills[e].link.push_back(s);
    }

    dfs(1);

    cout << m_max(Vills[1].DP[0], Vills[1].DP[1]);

    return 0;
}
```

DP를 설계하는데 있어서 주의할 점은 다음과 같았다.<br/>

- 우수마을이 아닌 곳은 반드시 우수마을 한 곳이랑 붙어있다.
- 우수마을인 곳의 다음 지점은 반드시 우수마을이 아니다.

그래서 코드 중에 이런 부분이 있다.

```
Vills[cur].DP[0] += m_max(Vills[Next].DP[0], Vills[Next].DP[1]);
Vills[cur].DP[1] += Vills[Next].DP[0];
```

DP[0]은 Cur 지점이 우수마을이 아닌 곳일 경우의 DP값이고,<br/>

DP[1]은 Cur 지점이 우수마을일 경우의 DP값이다.<br/>

Cur 지점이 우수마을이 아닐 경우 다음 마을에서 더 큰 DP값을 가져와서 저장해둔다.<br/>

이렇게 해서 1번지점이 시작점이니, 1번 지점에서의 DP값 중 더 큰 값을 출력하면 된다.<br/>

이번 문제는 다음 마을, Next 지점의 값을 가져오기 위해서 DP로 풀었어야 했던 문제였다.<br/>

