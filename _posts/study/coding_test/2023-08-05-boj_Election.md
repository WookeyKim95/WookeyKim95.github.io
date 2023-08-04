---
layout: post
title:  "[백준] 2660_회장뽑기 C++"
subtitle:   
date: 2023-08-05 07:38:14 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2660_회장뽑기 C++<br/>
<br/>

BFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct Person
{
    int num;
    bool visited = false;
    vector<int> link;
    int score = 0;

    Person(int _num) : num(_num) {};
};

vector<Person> People;
int N;
int MinScore = 51;

void setFalse()
{
    for (int i = 1; i <= N; ++i)
        People[i].visited = false;
    
    return;
}

int BFS(int start)
{
    queue<pair<int, int>> q;
    q.push({start, 0});
    People[start].visited = true;

    int ret = 0;
    while (!q.empty())
    {
        int Cur = q.front().first;
        int CurScore = q.front().second;
        q.pop();

        if (ret < CurScore)
            ret = CurScore;

        for (size_t i = 0; i < People[Cur].link.size(); ++i)
        {
            int Next = People[Cur].link[i];

            if (!People[Next].visited)
            {
                q.push({Next, CurScore + 1});
                People[Next].visited = true;
            }
        }
    }

    return ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i <= N; ++i)
    {
        Person P(i);
        People.push_back(P);
    }

    while (true)
    {
        int input1, input2;
        cin >> input1 >> input2;

        if (input1 == -1 && input2 == -1)
            break;

        People[input1].link.push_back(input2);
        People[input2].link.push_back(input1);
    }

    for (int i = 1; i <= N; ++i)
    {
        setFalse();
        int retScore = BFS(i);
        People[i].score = retScore;

        if (retScore < MinScore)
            MinScore = retScore;
    }

    vector<int> Candidate;

    for (int i = 1; i <= N; ++i)
    {
        if (People[i].score == MinScore)
            Candidate.push_back(i);
    }

    cout << MinScore << ' ' << Candidate.size() << '\n';

    for (size_t i = 0; i < Candidate.size(); ++i)
        cout << Candidate[i] << ' ';

    return 0;
}
```

BFS를 활용해보는 문제를 풀어보았다.<br/>

문제를 트리 형태로 해석해보면 결국엔 BFS를 실시했을 때 Depth가 가장 작은 사람이 회장이 되는 것이다.<br/>

내가 고민했던 부분은 DFS도 아니고 BFS를 실시하면서 가장 깊은 리프노드의 깊이를 어떻게 구할까? 였다.<br/>

CurScore를 활용하기로 하였다.<br/>

결국 자신의 자식노드들이 부모노드의 깊이 + 1을 가지고 있을테니까<br/>

자식노드에다가 부모노드의 점수 + 1을 짝지어서 반환하는 형태로 구현하였다.<br/>

즉, BFS에서 큐는 
```
pair<int, int>
```
위와 같은 자료형을 다루며<br/>

```
while (!q.empty())
{
    int Cur = q.front().first;
    int CurScore = q.front().second;
    q.pop();

    if (ret < CurScore)
        ret = CurScore;

    for (size_t i = 0; i < People[Cur].link.size(); ++i)
    {
        int Next = People[Cur].link[i];

        if (!People[Next].visited)
        {
            q.push({Next, CurScore + 1});
            People[Next].visited = true;
        }
    }
}
```

자식노드는 부모노드의 점수 + 1 값을 짝지어서 다루도록 구현한 것이다.<br/>

이렇게 해서 점수를 반환받고 MinScore을 가지고 있는 사람을<br/>

모으도록 구현하였다!<br/>