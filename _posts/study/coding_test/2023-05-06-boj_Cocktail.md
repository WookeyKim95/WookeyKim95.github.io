---
layout: post
title:  "[백준] 1033_칵테일 C++"
subtitle:   
date: 2023-04-26 06:47:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1033_칵테일 C++<br/>
<br/>

그래프 DFS를 사용하는 수학 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

#define pii pair<int, int>

using namespace std;

struct Ingredient
{
    int num;
    vector<int> link;
    int mass = 0;
    bool visited = false;

    Ingredient(int _num) : num(_num) {};
};

vector<Ingredient> Ingredients;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int gcd(int a, int b)
{
    int tmp;
    while (b != 0)
    {
        tmp = a % b;
        a = b;
        b = tmp;
    }
    return a;
}

void dfs(int cur, int val) {
    Ingredients[cur].visited = true;
    //비율 반영
    Ingredients[cur].mass *= val;
 
    for (size_t i = 0; i < Ingredients[cur].link.size(); ++i)
    {
        int next = Ingredients[cur].link[i];
        if (!Ingredients[next].visited)
            dfs(next, val);
    }

}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        Ingredient I(i);
        Ingredients.push_back(I);
    }

    for (int i = 0; i < N-1; ++i)
    {
        int a, b, p, q;
        cin >> a >> b >> p >> q;

        // 처음 방문했을 때
        if (Ingredients[a].mass == 0)
            Ingredients[a].mass = 1;
        if (Ingredients[b].mass == 0)
            Ingredients[b].mass = 1;

        int Gcd = gcd(m_max(Ingredients[a].mass, Ingredients[b].mass),
                    m_min(Ingredients[a].mass, Ingredients[b].mass));
        int a_mod = Ingredients[b].mass * p / Gcd;
        int b_mod = Ingredients[a].mass * q / Gcd;

        Gcd = gcd(m_max(a_mod, b_mod), m_min(a_mod, b_mod));

        for (size_t j = 0; j < Ingredients.size(); ++j)
            Ingredients[j].visited = false;
        
        // 전체 갱신
        dfs(a, a_mod / Gcd);
        dfs(b, b_mod / Gcd);

        Ingredients[a].link.push_back(b);
        Ingredients[b].link.push_back(a);
    }    

    for (size_t i = 0 ; i < Ingredients.size(); ++i)
        cout << Ingredients[i].mass << ' ';

    return 0;
}
```

대략적으로 노드를 연결해서 그래프 탐색을 통해 값을 갱신해야 하는 문제라는 것은 짐작했다.<br/>

즉, 노드 간의 관계를 GCD를 통해서 저장을 해두는 것이었다.<br/>

그런데 이 관계를 어떻게 저장할지를 고민했었다.<br/>

아! 관계를 저장한다기 보다는<br/>

입력을 받을 때마다 값을 갱신하고, 링크를 저장하는 방식으로 하면됐다.<br/>

값을 갱신하는 과정은 dfs를 이용하였다.<br/>