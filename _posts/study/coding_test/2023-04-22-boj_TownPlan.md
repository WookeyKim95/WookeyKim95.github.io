---
layout: post
title:  "[백준] 1647번_도시 분할 계획 C++"
subtitle:   
date: 2023-04-22 09:14:08 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1647번_도시 분할 계획 C++<br/>
<br/>

크루스칼 알고리즘, MST를 사용하는 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

#define MAX 100001

using namespace std;

int N, M;
vector<pair<int, pair<int, int>>> Roads;
vector<int> MST;
int Parent[MAX];
int ans = 0;

// 부모 찾기
int FindParent(int x)
{
    if (x == Parent[x])
        return x;
    else
    {
        int parent = FindParent(Parent[x]);
        Parent[x] = parent;
        return parent;
    }
}

// 같은 부모를 가진 노드 집합 합치기
void Union(int x, int y)
{
    x = FindParent(x);
    y = FindParent(y);

    if (x < y)
        Parent[y] = x;
    else
        Parent[x] = y;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    // 부모 테이블 초기화
    for (int i = 0; i <= N; ++i)
        Parent[i] = i;

    // 간선 삽입
    for (int i = 0 ; i < M; ++i)
    {
        int start, end, cost;
        cin >> start >> end >> cost;

        Roads.push_back({cost, {start, end}});
    }

    sort(Roads.begin(), Roads.end());

    for (size_t i = 0; i < Roads.size(); ++i)
    {
        int cost = Roads[i].first;
        int a = Roads[i].second.first;
        int b = Roads[i].second.second;

        // 사이클이 발생하지 않을 때에 MST에 포함하기
        // 즉, 같은 부모가 아닐 떄에만 MST에 포함함.
        if (FindParent(a) != FindParent(b))
        {
            // 같은 부모로 합치기!
            Union(a, b);

            // 그 뒤에 MST 벡터에 포함함
            MST.push_back(cost);
        }
    }

    for (size_t i = 0; i < MST.size() - 1; ++i)
        ans += MST[i];

    cout << ans;

    return 0;
}
```

솔직히 말해서 이 문제를 풀 당시에는<br/>

MST와 크루스칼 알고리즘을 혼자서 구현할 실력은 안됐었다.<br/>

그래서 연습을 하기 위해서 [참고링크](https://velog.io/@jxlhe46/%EB%B0%B1%EC%A4%80-1647%EB%B2%88.-%EB%8F%84%EC%8B%9C-%EB%B6%84%ED%95%A0-%EA%B3%84%ED%9A%8D)를 참고하여 풀었다.

간선들의 시작점, 끝점, 비용을 받음 다음에<br/>

비용이 낮은 순서대로 간선들을 정렬해준다.<br/>

그러고 나서 비용이 낮은 간선부터 차례대로 MST에 넣기 위한 작업을 하는데<br/>

여기서 부모가 같지 않아야 사이클에 포함이 될 수 있다.<br/>

유니온 파인드가 사용되는데 그 과정은 [참고링크](https://chanhuiseok.github.io/posts/algo-33/)를 참조하자.<br/>

이렇게 한 다음에 MST를 구하고, 마을을 두 곳으로 분할하면 되므로<br/>

마지막에 들어간 길이가 가장 긴 간선을 빼고 계산한다.<br/>