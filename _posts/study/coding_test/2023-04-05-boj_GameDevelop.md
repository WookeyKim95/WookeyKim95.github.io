---
layout: post
title:  "[백준] 1516번_게임 개발 C++"
subtitle:   
date: 2023-04-05 10:01:40 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1516번_게임 개발 C++<br/>
<br/>

BFS와 다이나믹 프로그래밍을 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <algorithm>

using namespace std;

struct building
{
    int num;
    vector<int> required;
    vector<int> child;
    int time;
    
    building(int _num, int _time)
    : num(_num), time(_time) {}
};

int N;
vector<building> buildings;
int AcculTime[502];

bool cmp(building a, building b);
void UnderConstruct(int start);
void Connect(int index);

int main()
{
    building padding(0, 0);
    buildings.push_back(padding);

    cin >> N;
    for (int i = 1; i <= N; ++i)
    {
        int InputTime;
        cin >> InputTime;
        
        building B(i, InputTime);
        while (true)
        {
            int InputRequired;
            
            cin >> InputRequired;
            if (InputRequired == -1)
                break;

            B.required.push_back(InputRequired);
        }

        sort(B.required.begin(), B.required.end());
        buildings.push_back(B);
    }

    for (int i = 1; i <= N; ++i)
        Connect(i);


    fill(AcculTime, AcculTime+502, 0);

    vector<int> roots;

    for (int i = 1; i <= N; ++i)
    {
        if (buildings[i].required.size() == 0)
            roots.push_back(i);
    }

    for (size_t i = 0; i < roots.size(); ++i)
        UnderConstruct(roots[i]);

    for (int i = 1; i <= N; ++i)
    {
        cout << AcculTime[i] << '\n';
    }

    return 0;
}

void UnderConstruct(int start)
{
    queue<int> q;
    q.push(start);
    AcculTime[start] = buildings[start].time;

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();

        for (size_t i = 0; i < buildings[Cur].child.size(); ++i)
        {
            int NextIndex = buildings[Cur].child[i];
            int NextConAccul = AcculTime[Cur] + buildings[NextIndex].time;
            if (NextConAccul > AcculTime[NextIndex])
            {
                AcculTime[NextIndex] = NextConAccul;
                q.push(NextIndex);
            }
        }
    }
}

void Connect(int index)
{
    for (size_t i = 0; i < buildings[index].required.size(); ++i)
    {
        buildings[buildings[index].required[i]].child.push_back(index);
    }
    return;
}
```

누적 건설시간 계산에는 다이나믹 프로그래밍을, 그래프 탐색에는 BFS를 사용하였다.<br/>

우선, 구조체 구성은 아래와 같다.

- 건물의 인덱스 번호
- 건물을 짓기 위해서 이전에 지어져야 하는 건물 번호들
- 건물의 그래프상 자식 건물 번호
- 건물을 짓는데 걸리는 시간

그래서 구성한 struct 코드가 아래와 같다.<br/>

```
struct building
{
    int num;
    vector<int> required;
    vector<int> child;
    int time;
    
    building(int _num, int _time)
    : num(_num), time(_time) {}
};
```

입력 받고 난 뒤에, BFS를 순회하기 위해서 자식 건물의 번호가 필요하다.<br/>

그래서 Connect함수를 구성하여 자식 건물이 몇번이 있는지를 연결하도록 구성하였다.<br/>

즉, 건물을 짓기 위해서 선행적으로 지어져야 하는 건물이 부모 건물이라면,<br/>

자식 건물은 그 반대 개념으로 작용하는 것이다.<br/>

```
void Connect(int index)
{
    for (size_t i = 0; i < buildings[index].required.size(); ++i)
    {
        buildings[buildings[index].required[i]].child.push_back(index);
    }
    return;
}
```

그리고 우선 roots벡터를 만들고, required의 크기가 0인 노드들을 넣어주었다.

후에 BFS탐색을 할 때 루트노드부터 탐색을 할 수 있도록 만들기 위함이다.<br/>

```
    vector<int> roots;

    for (int i = 1; i <= N; ++i)
    {
        if (buildings[i].required.size() == 0)
            roots.push_back(i);
    }

    for (size_t i = 0; i < roots.size(); ++i)
        UnderConstruct(roots[i]);

```

그리고 fill함수를 이용해서 다이나믹 프로그래밍에 사용할 AcculTime 배열을 0으로 초기화한 후,<br/>

BFS를 이용해서 루트노드부터 BFS 탐색을 할 수 있도록 하였다.<br/>

루트노드가 여러개일 수 있기 때문에 for문을 사용하여 루트노드부터 순차적으로 탐색하도록 구현하였다.<br/>

그리고 BFS를 할 때, <br/>

(자식 노드의 AcculTime 값 = 부모 노드의 AcculTime 값 + 자식 노드의 건물을 짓는 시간)이다.<br/>

만일 이미 자식노드의 AcculTime 값이 계산되었다면<br/>

더 큰 값을 덮어쓰고, 자식을 탐색하도록 구현하였다.<br/>

```
void UnderConstruct(int start)
{
    queue<int> q;
    q.push(start);
    AcculTime[start] = buildings[start].time;

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();

        for (size_t i = 0; i < buildings[Cur].child.size(); ++i)
        {
            int NextIndex = buildings[Cur].child[i];
            int NextConAccul = AcculTime[Cur] + buildings[NextIndex].time;
            if (NextConAccul > AcculTime[NextIndex])
            {
                AcculTime[NextIndex] = NextConAccul;
                q.push(NextIndex);
            }
        }
    }
}
```

이렇게 해서 BFS와 다이나믹 프로그래밍을 이용해서 문제 풀이를 구현하였다.<br/>