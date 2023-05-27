---
layout: post
title:  "[백준] 1202_보석 도둑 C++"
subtitle:   
date: 2023-05-27 09:50:01 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1202_보석 도둑 C++<br/>
<br/>

우선순위 큐와 정렬을 이용한 그리디 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <queue>

using namespace std;

struct Jewel
{
    int price;
    int weight;
};

int N, K;
vector<Jewel> Jewels;
vector<int> bags;
priority_queue<int> pq;
long long ans = 0;

bool asc(Jewel a, Jewel b)
{
    return a.weight < b.weight;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> K;

    for (int i = 0; i < N; ++i)
    {
        int p, w;
        cin >> w >> p;

        Jewel j;
        j.price = p;
        j.weight = w;

        Jewels.push_back(j);
    }

    for (int i = 0; i < K; ++i)
    {
        int b;
        cin >> b;
        bags.push_back(b);
    }

    sort(Jewels.begin(), Jewels.end(), asc);
    sort(bags.begin(), bags.end());

    int idx = 0;
    for (size_t i = 0; i < bags.size(); ++i)
    {
        while (idx < N && Jewels[idx].weight <= bags[i])
            pq.push(Jewels[idx++].price);

        if (!pq.empty())
        {
            ans += pq.top();
            pq.pop();
        }
    }

    cout << ans;

    return 0;
}
```

<br/>
<Br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <queue>

using namespace std;

struct Jewel
{
    int price;
    int weight;
};

int N, K;
vector<Jewel> Jewels;
priority_queue<int> bags;
int ans = 0;

bool desc(Jewel a, Jewel b)
{
    if (a.price == b.price)
        return a.weight > b.weight;
    
    return a.price > b.price;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> K;

    for (int i = 0; i < N; ++i)
    {
        int p, w;
        cin >> w >> p;

        Jewel j;
        j.price = p;
        j.weight = w;

        Jewels.push_back(j);
    }

    for (int i = 0; i < K; ++i)
    {
        int b;
        cin >> b;
        bags.push(b);
    }

    sort(Jewels.begin(), Jewels.end(), desc);
    
    bool flag = true;

    while (flag && !bags.empty())
    {
        int CurBag = bags.top();

        for (size_t i = 0; i < Jewels.size(); ++i)
        {
            if (Jewels[i].weight <= CurBag)
            {
                ans += Jewels[i].price;
                bags.pop();
                Jewels.erase(Jewels.begin() + i);
                break;
            }

            if (i == Jewels.size() - 1)
                flag = false;
        }
    }

    cout << ans;

    return 0;
}
```
<br/>

문제를 보자마자 그리디라고 생각했고,<br/>

곧바로 정렬을 할 준비를 했다.<br/>

다만 고민이었던 것이 보석을 담은 가방을 배열에서 어떻게 제외시킬지 고민을 했다.<br/>

처음엔 우선순위큐를 사용하였다.<br/>

되도록이면 가격이 높고, 무거운 보석을 용량이 큰 가방에 담는 것이 가장 이득이라 생각했다.<Br/>

그래서 가방이 용량이 높은 순서대로 정렬되도록 우선순위 큐를 구현하였다.<br/>

또한, 가격이 높은 보석이 먼저, 가격이 같으면 무게가 무거운 보석이 먼저 나오게 정렬되도록 구현하였다.<br/>

가방에 보석을 담았으면 큐에서 가방이 제외되도록 구현하였다.<br/>

```
if (i == Jewels.size() - 1)
    flag = false;
```
남은 가방들 중에서 보석을 더이상 담을 수 없으면 flag = false로 해서 큐를 빠져나오도록 했다.<br/>

하지만 오답이었다.<br/>

이 방법이 유효하지는 않았던 것 같다.<br/>
<br/>

## 정답코드로의 변환<br/>
<Br/>

[참고 링크](https://jaimemin.tistory.com/760)를 참조하여 코드를 고쳐보기로 하였다.<br/>

우선, bags도 vector로 고치고 오름차순으로 정렬하였고,<br/>

보석도 무게 기준으로 오름차순으로 정렬하도록 바꿔주었다.<br/>

```
bool asc(Jewel a, Jewel b)
{
    return a.weight < b.weight;
}

...

sort(Jewels.begin(), Jewels.end(), asc);
sort(bags.begin(), bags.end());
```

참고 링크의 필자께서는 우선순위 큐를 최대 힙을 사용하듯이 사용하셨다.<br/>

그래서 담을 수 있는 보석을 골라서 그 가격을 우선순위 큐에 넣는 형식으로 구현하셨다.<br/>

```
for (size_t i = 0; i < bags.size(); i++)
{
    while (idx < N && Jewels[idx].weight <= bags[i])
        pq.push(Jewels[idx++].price);

    if (!pq.empty())
    {
        ans += pq.top();
        pq.pop();
    }
}
```