---
layout: post
title:  "[백준] 20040_사이클 게임 C++"
subtitle:   
date: 2023-07-04 08:05:52 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 20040_사이클 게임 C++<br/>
<br/>

유니온 파인드를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>

using namespace std;

int N, M;
int parent[500000];
int ans = 0;

int find(int a)
{
	if (parent[a] == a)
        return a;
	else 
        return parent[a] = find(parent[a]); 
}

bool uni(int a, int b)
{
	a = find(a); 
	b = find(b); 

    if (a == b)
        return true;
    else
    {
        parent[b] = a;
        return false;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
        parent[i] = i;

    for (int i = 1; i <= M; ++i)
    {
        int s, e;
        cin >> s >> e;

        if (uni(s, e))
        {
            ans = i;
            break;
        }
    }

    cout << ans;

    return 0;
}
```

[유니온 파인드 내용 참고 링크](https://ssungkang.tistory.com/198)<br/>

예전 같았으면 사이클을 이루는지 판단하려면 보통 BFS를 먼저 생각했을 것이다.<br/>

두번 방문 되는 것이 생기면 사이클을 이룬다고 판단을 하는 것이지.<br/>

하지만, 제한시간이 1초라면 일일이 확인하는 것은 불가능하다.<br/>

그렇다면 빠르게 사이클을 판단하는 방법은 뭐가 있을까?<br/>

그것은 바로 유니온 파인드(Union Find)이다.<br/>

서로 연결된 지점을 만난다면 부모를 같게 만들어주면서<br/>

사이클 여부를 판단하는 방법이다.<br/>

크루스칼 알고리즘을 활용한 최소 스패닝 트리 제작에도 많이 쓰이는 방법이다.<br/>

사이클을 이룬다면 유니온을 시키지 않았는데도 최종 부모가 같은 순간이 오기 때문에<br/>

이를 이용해서 사이클 여부를 판별할 수 있다.<br/>

