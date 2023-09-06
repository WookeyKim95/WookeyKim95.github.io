---
layout: post
title:  "[백준] 17398_통신망 분할 C++"
subtitle:   
date: 2023-08-25 07:17:58 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17398_통신망 분할 C++<br/>
<br/>

유니온 파인드를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <stack>

using namespace std;

#define MAX 100001

struct Tower
{
    int parent;
    int Count;
};

int N, M, Q;
vector<pair<int, int>> Edges;
vector<Tower> Towers;
stack<int> st;
long long ans = 0;
bool ToCut[MAX];

int Find(int a)
{
    if (Towers[a].parent == a)
        return a;
    return Towers[a].parent = Find(Towers[a].parent);
}

void Union(int a, int b)
{
    int x = Find(a);
    int y = Find(b);

    if (x < y)
    {
        Towers[x].parent = y;
        Towers[y].Count += Towers[x].Count;
        Towers[x].Count = 0;
    }
        
    else
    {
        Towers[y].parent = x;
        Towers[x].Count += Towers[y].Count;
        Towers[y].Count = 0;
    }
        
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> Q;

    for (int i = 0; i <= N; ++i)
    {
        Tower t;
        t.parent = i;
        t.Count = 1;
        Towers.push_back(t);
    }

    Edges.push_back({0, 0});
    for (int i = 0; i < M; ++i)
    {
        int s, e;
        cin >> s >> e;
        Edges.push_back({s, e});
    }

    for (int i = 0; i < Q; ++i)
    {
        int _input;
        cin >> _input;
        ToCut[_input] = true;
        st.push(_input);
    }

    for (size_t i = 1; i < Edges.size(); ++i)
    {
        if (!ToCut[i])
        {
            int X = Edges[i].first;
            int Y = Edges[i].second;

            if (Find(X) != Find(Y))
                Union(X, Y);
        }
    }

    while(!st.empty())
    {
        int a = st.top();
        st.pop();

        int X = Edges[a].first;
        int Y = Edges[a].second;

        int PX = Find(X);
        int PY = Find(Y);

        if (PX != PY)
        {
            ans += (Towers[PX].Count * Towers[PY].Count);
            Union(PX, PY);
        }
    }
    
    cout << ans;

    return 0;
}
```

통신망 분할이고 그룹을 따진다고 하기에 유니온 파인드를 쓴다는 것은 눈치챘다.<br/>

그래서 유니온 파인드 코드를 구성했지만 이를 어떻게 활용할지는 감이 오지 않았다.<br/>

그래서 구글링을 통해 이번엔 [이분의 글](https://littlesam95.tistory.com/169)을 참고하기로 하였다.<br/>

요약을 하면<br/>

- 분할의 반대는 연결이다.
- 분할을 하는 순서의 역순대로 연결을 진행시킨다.
- 그 과정에서 나오는 비용을 더해주면 된다.

즉, 모든 분할이 완료된 상태의 통신망을 구성해준 뒤에<br/>

분할의 역순으로 연결을 진행해주면 되는 것이다.<br/>

그리고 모든 탑이 하나의 그룹이 된 시점에는 비용이 발생하지 않는다.<br/>

그리고 그룹에 속해있는 카운트를 세어주는 방법은<br/>

```
if (x < y)
{
    Towers[x].parent = y;
    Towers[y].Count += Towers[x].Count;
    Towers[x].Count = 0;
}
    
else
{
    Towers[y].parent = x;
    Towers[x].Count += Towers[y].Count;
    Towers[y].Count = 0;
}
```
Union을 하는 과정에서 자신을 부모로 두고있는 탑의 개수를 조작하면 되는 것이었다.<br/>

이런 생각을 어떻게 하신걸까 궁금해진다..<br/>

자 그래서 스택에다가 분할 순서를 역순으로 저장해서 순서대로 pop을 해주니<br/>

비용이 나오게 되었다!<br/>