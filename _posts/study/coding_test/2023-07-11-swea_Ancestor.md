---
layout: post
title:  "[SWEA] 1248_공통조상 C++"
subtitle:   
date: 2023-07-11 07:23:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [SWEA] 1248_공통조상 C++<br/>
<br/>

부모를 찾아내고, 탐색과정에서 DFS를 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <queue>
#include <algorithm>

using namespace std;

struct Node
{
    int parent;
    vector<int> child;
    bool visited = false;

    Node(int _input) : parent(_input) {};
};

vector<Node> Nodes;
int V, E, N1, N2;
bool found = false;

int CalTree(int a)
{
    int ret = 1;

    queue<int> q;
    q.push(a);

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();
        
        for (size_t i = 0; i < Nodes[Cur].child.size(); ++i)
        {
            q.push(Nodes[Cur].child[i]);
            ret++;
        }
    }

    return ret;
}

void DFS(int target, int Cur)
{
    if (Cur == target)
    {
        found = true;
        return;
    }

    for (size_t i = 0; i < Nodes[Cur].child.size(); ++i)
    {
        int Next = Nodes[Cur].child[i];

        if (!Nodes[Next].visited)
        {
            Nodes[Next].visited = true;
            DFS(target, Next);
            Nodes[Next].visited = false;
        }
    }
}

int FindParent(int a, int b)
{
    int CurA_Parent = a;

    while (CurA_Parent != 1)
    {
        DFS(b, CurA_Parent);

        if (found)
            return CurA_Parent;
        
        CurA_Parent = Nodes[CurA_Parent].parent;
    }

    return CurA_Parent;
}

int main(int argc, char** argv)
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

	int test_case;
	int T;

	cin>>T;

	for (test_case = 1; test_case <= T; ++test_case)
	{
        cin >> V >> E >> N1 >> N2;
        found = false;

        Nodes.clear();

        for (int i = 0; i <= V; ++i)
        {
            Node n(i);
            Nodes.push_back(n);
        }

        for (int i = 0; i < E; ++i)
        {
            int Par, Chi;
            cin >> Par >> Chi;

            Nodes[Par].child.push_back(Chi);
            Nodes[Chi].parent = Par;
        }

        int NewRoot = FindParent(N1, N2);

        cout << '#' << test_case << ' ' << NewRoot << ' ' << CalTree(NewRoot) << '\n';

	}
	return 0;
}
```

공통조상을 찾아내는 과정에서 다소 애를 먹었다.<br/>

공통조상을 찾아내는 방법은 아래와 같다.<br/>

- 한 노드에서 시작한다.
- 부모를 찾아 거슬러 올라가면서 DFS를 실시한다.
- DFS를 통해서 노드 B를 찾으면 그 부모가 공통조상이므로 거슬러 올라가는 과정을 중단한다.



이를 코드로 구현한 부분이 여기이다.<br/>

```
void DFS(int target, int Cur)
{
    if (Cur == target)
    {
        found = true;
        return;
    }

    for (size_t i = 0; i < Nodes[Cur].child.size(); ++i)
    {
        int Next = Nodes[Cur].child[i];

        if (!Nodes[Next].visited)
        {
            Nodes[Next].visited = true;
            DFS(target, Next);
            Nodes[Next].visited = false;
        }
    }
}

int FindParent(int a, int b)
{
    int CurA_Parent = a;

    while (CurA_Parent != 1) // 루트노드까지 계속 거슬러 올라간다.
    {
        DFS(b, CurA_Parent);

        if (found) // 부모를 찾았을 경우, 전역변수에 있는 found가 true가 되어 수색 중단.
            return CurA_Parent;
        
        CurA_Parent = Nodes[CurA_Parent].parent;
    }

    return CurA_Parent; // 공통 조상을 return.
}
```

그리고 공통 조상을 찾아냈으면 tree를 계산해야하는데<br/>

나는 그 과정은 BFS를 이용했다.<br/>

BFS를 통해 노드 하나를 찾을 때마다 개수를 하나씩 더하고,<Br/>

BFS가 완료되면 개수를 return하는 것이다.<br/>

그 부분이 바로 아래 코드이다.<br/>

```
int CalTree(int a)
{
    int ret = 1;

    queue<int> q;
    q.push(a);

    while (!q.empty())
    {
        int Cur = q.front();
        q.pop();
        
        for (size_t i = 0; i < Nodes[Cur].child.size(); ++i)
        {
            q.push(Nodes[Cur].child[i]);
            ret++;
        }
    }

    return ret;
}
```

그리고 받아낸 값을 출력하면 끝!<br/>