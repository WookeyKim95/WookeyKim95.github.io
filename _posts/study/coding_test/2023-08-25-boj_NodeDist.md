---
layout: post
title:  "[백준] 1761_정점들의 거리 C++"
subtitle:   
date: 2023-08-25 06:50:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1761_정점들의 거리 C++<br/>
<br/>

트리 부모 탐색에 대해서 알아보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

#define MAXHEIGHT 16

struct Node
{
    vector<pair<int, int>> link;
    bool visited = false;
    int parent[MAXHEIGHT] = {0};
    int depth;
    int dist[MAXHEIGHT] = {0};
};

vector<Node> Nodes;
int N, M;

void DFS(int start, int lev)
{
    Nodes[start].visited = true;
    Nodes[start].depth = lev;

    for (size_t i = 0; i < Nodes[start].link.size(); ++i)
    {
        int Next = Nodes[start].link[i].first;
        int NextCost = Nodes[start].link[i].second;

        if (!Nodes[Next].visited)
        {
            Nodes[Next].parent[0] = start;
            Nodes[Next].dist[0] = NextCost;
            DFS(Next, lev+1);
        }
    }
}

void connect()
{
	for (int i = 1; i < MAXHEIGHT; ++i)
    {
		for (int Cur = 1; Cur <= N; ++Cur)
        {
			int prevParent = Nodes[Cur].parent[i-1];
			Nodes[Cur].parent[i] = Nodes[prevParent].parent[i-1];
			
			if(Nodes[prevParent].parent[i-1] == 0)
                continue;
			
			int prevDist = Nodes[Cur].dist[i-1];
			Nodes[Cur].dist[i] = prevDist + Nodes[prevParent].dist[i-1];
		}
	}
}

int LCA(int xNode, int yNode)
{
	if(Nodes[xNode].depth > Nodes[yNode].depth)
    {
		int temp = xNode;
		xNode = yNode;
		yNode = temp;
	}

	int lenSum = 0;

	for (int i = MAXHEIGHT-1; i >= 0; --i)
    {
		int jump = 1 << i;
		if(Nodes[yNode].depth - Nodes[xNode].depth >= jump)
        {
			lenSum += Nodes[yNode].dist[i];
			yNode = Nodes[yNode].parent[i];
		}
	}

	if(xNode == yNode)
        return lenSum;

	for (int i = MAXHEIGHT-1; i >= 0; --i)
    {
		if(Nodes[xNode].parent[i] == Nodes[yNode].parent[i])
            continue;
		
		lenSum += Nodes[xNode].dist[i];
		xNode = Nodes[xNode].parent[i];
		
		lenSum += Nodes[yNode].dist[i];
		yNode = Nodes[yNode].parent[i];
	}
	
	lenSum += Nodes[xNode].dist[0] + Nodes[yNode].dist[0];
	return lenSum;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i <= N; ++i)
    {
        Node n;
        Nodes.push_back(n);
    }

    for (int i = 0; i < N-1; ++i)
    {
        int s, e, c;
        cin >> s >> e >> c;

        Nodes[s].link.push_back({e, c});
        Nodes[e].link.push_back({s, c});
    }
    
    DFS(1, 0);
    connect();

    cin >> M;

    for (int i = 0; i < M; ++i)
    {
        int x, y;
        cin >> x >> y;
        cout << LCA(x, y) << '\n';
    }

    return 0;
}
```

후술할 코드와 로직으로 시도를 했으나 시간초과가 났다.<br/>

최소 공통 조상 파트는 배우지 않은 부분이라 그런지 아이디어가 떠오르지 않아서<br/>

구글링을 했고, [참고링크](https://velog.io/@brol2/BOJ-1761%EB%B2%88-%EC%A0%95%EC%A0%90%EB%93%A4%EC%9D%98-%EA%B1%B0%EB%A6%AC)를 참고하여 풀었다.<br/>

아하! 일일이 BFS를 하는 것이 아니라 처음에 입력을 받고 기록을 한번만 하면서<br/>

관계를 지정해주고 입력받은 관계를 바로바로 출력하는 구조인 것이다.<br/>

이렇게 해서 시간복잡도를 줄일 수 있다.<br/>

후술할 코드는 입력을 받을 때마다 부모를 찾으러가고.. BFS를 하고.. 그러니<br/>

시간이 오래 걸릴 수 밖에.<br/>

<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

struct Node
{
    vector<pair<int, int>> link;
    bool visited = false;
    pair<int, int> parent;
};

vector<Node> Nodes;
int N, M;

int FindNode(int a, int target)
{
    queue<pair<int, int>> q;
    q.push({a, 0});
    Nodes[a].visited = true;

    while(!q.empty())
    {
        int Cur = q.front().first;
        int CurDist = q.front().second;
        q.pop();

        if (Cur == target)
            return CurDist;

        for (size_t i = 0; i < Nodes[Cur].link.size(); ++i)
        {
            int Next = Nodes[Cur].link[i].first;
            int NextCost = Nodes[Cur].link[i].second;

            if (!Nodes[Next].visited)
            {
                Nodes[Next].visited = true;
                q.push({Next, CurDist + NextCost});
            }
        }
    }

    return 0;
}

void GoParent(int CurNode, int target, int CurDist)
{
    int ans = CurDist;

    if (CurNode == target)
    {
        cout << CurDist << '\n';
        return;
    }

    for (int j = 1; j <= N; ++j)
        Nodes[j].visited = false;

    int ret = FindNode(CurNode, target);
    if (ret)
    {
        ans += ret;
        cout << ans << '\n';
        return; 
    }
    else
        GoParent(Nodes[CurNode].parent.first, target, CurDist + Nodes[CurNode].parent.second);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i <= N; ++i)
    {
        Node n;
        n.parent.first = i;
        n.parent.second = 0;
        Nodes.push_back(n);
    }

    for (int i = 0; i < N-1; ++i)
    {
        int s, e, c;
        cin >> s >> e >> c;

        Nodes[s].link.push_back({e, c});
        Nodes[e].link.push_back({s, c});

        Nodes[e].parent.first = s;
        Nodes[e].parent.second = c;
    }

    cin >> M;

    for (int i = 0; i < M; ++i)
    {
        int s, e;
        cin >> s >> e;

        GoParent(s, e, 0);
    }

    return 0;
}
```

구현을 하면서 느낀게, 이 문제에서 간선의 개수가 N-1개이기 때문에<br/>

서로 부모가 누구인지 혼선을 빚을 일이 없다는 것이 다행으로 느껴졌다.<br/>

사실 이 문제를 풀면서 부모를 어떻게 기록해야하는가? 를 제일 고민했다.<br/>

작은 수를 부모로 취급할까?하니 프로그램이 터져버렸다.<br/>

만에 하나 이런 경우가 나올까봐 그랬다.<br/>

```
// N = 3
1 2
3 1
// M = 1
3 2
```

내가 의도한 것은 루트가 1이고 자식 노드가 각각 2와 3인 트리를 의도한 것이다.<br/>

그런데 연결여부만 표시되어있고 어느 쪽이 부모인지 입력에서는 불분명하니까<br/>

이것을 내가 정해줘야 한다는 점이 힘들었다.<br/>

일단은 테스트케이스의 경우는 정답으로 출력이 된다!<br/>

구현은 어떻게 했는지 살펴보자.<br/>

```
void GoParent(int CurNode, int target, int CurDist)
{
    int ans = CurDist;

    if (CurNode == target)
    {
        cout << CurDist << '\n';
        return;
    }

    for (int j = 1; j <= N; ++j)
        Nodes[j].visited = false;

    int ret = FindNode(CurNode, target);
    if (ret)
    {
        ans += ret;
        cout << ans << '\n';
        return; 
    }
    else
        GoParent(Nodes[CurNode].parent.first, target, CurDist + Nodes[CurNode].parent.second);
}
```

입력인자로 현재 노드, 목표 지점, 시작점으로부터 현재까지 거리 를 입력받는다.<br/>

만약 노드를 찾는데 올라오기만 했으면서 목표 노드에 도달했다면 이 코드를 통해 정답을 출력한다.<br/>

```
if (CurNode == target)
{
    cout << CurDist << '\n';
    return;
}
```

그렇지 않다면 BFS를 수행하면서 원하는 목표노드가 자식에 있는지 탐색한다.<br/>

있다면 ret 값이 0이 아닐 것이니 BFS를 통해 찾은 값을 더하고 출력시킨다.<br/>

없다면 GoParent를 재귀호출해서 한칸 더 올라간다.<br/>

목표노드를 찾을 때 까지 반복하는 구조로 구현하였다.<br/>

하지만 노드를 올라갈 때마다 BFS를 해야하기 때문에<br/>

최악의 경우에는 O(V^3) 이라는 어마무시한 시간복잡도가 출력되고,<br/>

그 결과 시간초과가 났다.<br/>

아이디어가 떠오르지 않아서 구글링을 했다.<br/>