---
layout: post
title:  "[백준] 11725_트리의 부모 찾기 C++"
subtitle:   
date: 2023-06-21 07:56:17 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11725_트리의 부모 찾기 C++<br/>
<br/>

DFS나 BFS를 활용해볼 수 있는 트리 탐색 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct Node
{
    int Parent;
    vector<int> link;
    bool visited = false;
};

int N;
vector<Node> Nodes;

void BFS()
{
    queue<int> q;
    q.push(1);
    Nodes[1].visited = true;

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();

        for (size_t i = 0; i < Nodes[Cur].link.size(); ++i)
        {
            int Next = Nodes[Cur].link[i];
            if (!Nodes[Next].visited)
            {
                Nodes[Next].visited = true;
                Nodes[Next].Parent = Cur;
                q.push(Next);
            }
        }
    }
}

int main()
{
    cin >> N;

    for (int i = 0; i <= N; ++i)
    {
        Node N;
        if (i == 1)
            N.Parent = 0;
        
        Nodes.push_back(N);
    }

    for (int i = 0; i < N-1; ++i)
    {
        int _input1, _input2;
        cin >> _input1 >> _input2;

        Nodes[_input1].link.push_back(_input2);
        Nodes[_input2].link.push_back(_input1);
    }

    BFS();

    for (int i = 2; i <= N; ++i)
        cout << Nodes[i].Parent << '\n';

    return 0;
}
```
<br/>

그래프를 탐색하는 과정에서 부모를 찾는 문제이다.<br/>

DFS랑 BFS를 활용할 수 있다고 생각했는데 나는 BFS를 사용하였다.<br/>

생각하기가 더 쉬워서 그랬던 것 같다 ㅋㅋ<br/>

우선, 노드 구성은 아래와 같이 해준 뒤 입력 값을 받았다.<br/>

```
struct Node
{
    int Parent;
    vector<int> link;
    bool visited = false;
};
```

그리고 BFS는 아래와 같이 설계했다.<br/>

```
void BFS()
{
    queue<int> q;
    q.push(1);
    Nodes[1].visited = true;

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();

        for (size_t i = 0; i < Nodes[Cur].link.size(); ++i)
        {
            int Next = Nodes[Cur].link[i];
            if (!Nodes[Next].visited)
            {
                Nodes[Next].visited = true;
                Nodes[Next].Parent = Cur;
                q.push(Next);
            }
        }
    }
}
```

루트 노드가 1이니 무조건 1번 부터 시작하도록 하였고,<br/>

어떤 노드에 자리 잡으면 자식 노드를 순차적으로 탐색한다.<br/>

그 과정에서 Next 노드의 부모는 Cur이라고 적어줌으로써<br/>

부모노드 찾는 과정을 구현하였다.<br/>

그리고 마지막 과정으로 기록한 부모노드를 출력하면 끝!<br/>