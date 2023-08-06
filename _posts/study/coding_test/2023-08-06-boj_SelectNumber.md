---
layout: post
title:  "[백준] 2668_숫자고르기 C++"
subtitle:   
date: 2023-08-06 07:25:38 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2668_숫자고르기 C++<br/>
<br/>

Set과 DFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <set>
#include <cstring>

using namespace std;

int number[101];
int N;
bool visited[101];
bool OK;
set<int> group;

void DFS(int first, int num)
{
    if (visited[num])
    {
        if (first == num)
        {
            OK = true;
            group.insert(num);
        }
        return;
    }

    visited[num] = true;
    DFS(first, number[num]);
    if (OK)
    {
        group.insert(num);
		group.insert(number[num]);
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 1; i <= N; ++i)
        cin >> number[i];

    for (int i = 1; i <= N; ++i)
    {
		visited[i] = true;
		DFS(i, number[i]);
		memset(visited, false, 101);
		OK = false;
	}

	cout << group.size() << '\n';
	
    for (auto i : group)
		cout << i << '\n';



    return 0;
}
```

경우의 수를 따지기 위해서 DFS를 활용해보는 문제다.<br/>

우선 N의 최대값이 100이기 때문에 충분히 vector을 안써도 될 것이라 판단이 됐다.<br/>

set에서 숫자를 빼는 경우는 없다. 왜냐하면 최대한 큰 합을 구해야하니까.<br/>

DFS를 통해서 탐색을 해주며, 탐색을 했는데 지금까지 탐색했던 경로에 있는 숫자들이<br/>

조건에 부합하는 경우 OK를 true로 해주고, 다시 돌아가면서<br/>

숫자를 set에 넣는 식으로 구현하였다.<br/>

그리고 이 과정을 모든 숫자를 시작점으로 해서 탐색을 하도록 조치하였다.<br/>

그렇게 되면 최대한 많은 쌍을 구할 수 있을 것이다.<br/>