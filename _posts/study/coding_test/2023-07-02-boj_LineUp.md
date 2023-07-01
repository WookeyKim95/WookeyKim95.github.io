---
layout: post
title:  "[백준] 2252_줄 세우기 C++"
subtitle:   
date: 2023-07-02 07:44:54 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2252_줄 세우기 C++<br/>
<br/>

위상정렬을 배워보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <queue>
#include <vector>

#define MAX 100001

using namespace std;

int N, M;
int Entry[MAX];
vector<int> Edge[MAX];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < M; ++i)
    {
        int a, b;
        cin >> a >> b;

        Entry[b]++;
        Edge[a].push_back(b);
    }

    queue<int> q;

    for (int i = 1; i <= N; ++i)
    {
        if (Entry[i] == 0)
            q.push(i);
    }

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();
 
        cout << Cur << ' ';
        for (size_t i = 0; i < Edge[Cur].size(); ++i)
        {
            int Next = Edge[Cur][i];
            Entry[Next]--;
 
            if (Entry[Next] == 0)
                q.push(Next);
        }
    }

    return 0;
}
```

위상정렬을 이용해 줄을 세워보는 문제였다.<br/>

이번 문제는 [이분의 글](https://yabmoons.tistory.com/409)을 통해 공부를 하면서 풀었다.<br/>

위상정렬이란, 일의 순서를 정렬하는 방법이라고 한다.<br/>

우선순위를 알고 있을 때, 어떤 순서대로 일을 진행해야하는지 정하는 정렬인 것이다.<br/>

케이스 1을 예로 들어 이 코드의 의미를 살펴보면<br/>

```
3 2
1 3
2 3
```
이 있다고 하면, 3번이 줄을 설 수 있는 때는 1번과 2번이 모두 줄을 선 뒤이다.<br/>

그래서 답은 1, 2, 3 혹은 2, 1, 3 이렇게 될 수 있다.<br/>

위 코드에 따르면 답은 1, 2, 3 이렇게 출력될 것이다.<br/>

즉 Entry[a] = b의 의미는 a의 앞에 b명을 줄세워야한다는 의미가 된다.<br/>

큐를 통해서 줄을 세우면서 Entry[a]의 값이 0이 되면 그때 a가 줄을 설 수 있게 되는 것이다.<br/>

```
for (size_t i = 0; i < Edge[Cur].size(); ++i)
{
    int Next = Edge[Cur][i];
    Entry[Next]--;

    if (Entry[Next] == 0)
        q.push(Next);
}
```

줄을 세운다면 for문을 통해서 값을 하나씩 줄여주는데 그 과정이 위의 코드이다.<br/>

이 괒어을 통해서 Entry[Next]가 0이 된다면 Next번 학생이 줄을 설 수 있게 되는 것!<br/>