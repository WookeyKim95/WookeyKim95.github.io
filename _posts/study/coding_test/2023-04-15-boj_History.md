---
layout: post
title:  "[백준] 1613번_역사 C++"
subtitle:   
date: 2023-04-15 10:01:10 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1613번_역사 C++<br/>
<br/>

플로이드-워셜 알고리즘을 사용하는 문제.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int N, K, M;
int Floid[401][401] = {0,};

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    memset(Floid, 0, sizeof(Floid));

    cin >> N >> K;

    for (int i = 0; i < K; ++i)
    {
        int A, B;
        cin >> A >> B;

        Floid[A][B] = -1;
        Floid[B][A] = 1;
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            for (int k = 1; k <= N; ++k)
            {
                if (Floid[j][k] == 0)
                {
                    if (Floid[j][i] == 1 && Floid[i][k] == 1)
                        Floid[j][k] = 1;
                    else if (Floid[j][i] == -1 && Floid[i][k] == -1)
                        Floid[j][k] = -1;
                }
            }
        }
    }

    cin >> M;

    for (int i = 0; i < M; ++i)
    {
        int A, B;
        cin >> A >> B;

        cout << Floid[A][B] << '\n';
    }

    return 0;
}
```

플로이드-워샬 알고리즘에 대해서 몰랐었는데 이참에 공부를 해야겠다.<br/>

<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <set>
#include <cstring>

using namespace std;

struct Fact
{
    int num;
    set<int> prev;
    set<int> next;
    Fact(int _num) : num(_num) {}
};

int N, K, M;
vector<Fact> Facts;
int Floid[401][401] = {0,};

void Floid_Warshall_prev(set<int> a, int b)
{
    for (auto iter : a)
    {
        Floid[iter][b] = -1;
        Floid[b][iter] = 1;
        Floid_Warshall_prev(Facts[iter].prev, b);
    }
    return;
}

void Floid_Warshall_next(set<int> a, int b)
{
    for (auto iter : a)
    {
        Floid[iter][b] = -1;
        Floid[b][iter] = 1;
        Floid_Warshall_next(Facts[iter].next, b);
    }
    return;
}

void Floid_Warshall()
{
    for (int i = 1; i <= N; ++i)
    {
        Floid_Warshall_next(Facts[i].next, i);
        Floid_Warshall_prev(Facts[i].prev, i);
    }
}

int main()
{
    cin >> N >> K;
    Fact padding(0);
    Facts.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        Fact f(i);
        Facts.push_back(f);
    }

    for (int i = 0; i < K; ++i)
    {
        int A, B;
        cin >> A >> B;

        Facts[A].next.insert(B);
        Facts[B].prev.insert(A);
    }

    memset(Floid, 0, sizeof(Floid));
    Floid_Warshall();

    cin >> M;

    for (int i = 0; i < M; ++i)
    {
        int A, B;
        cin >> A >> B;

        cout << Floid[A][B] << '\n';
    }

    return 0;
}
```

어떤 루트노드가 있다면 그 노드의 자식 노드를 모두 탐색하면서 관계를 기록하는 Floid배열을 만들었다.<br/>

그래서 역사관계의 전후관계를 1과 -1로 기록하도록 구현하였고,<br/>

기록된 값을 출력하도록 하였다.<br/>

하지만 탐색 속도를 높이기 위해 set자료구조를 사용했음에도 불구하고<br/>

기록과정이 너무 길어서 시간초과가 떠버렸다.<br/>

해결 방안이 떠오르지 않아 [참고링크](https://yabmoons.tistory.com/107)를 참고하였다.<br/>

아하! 내가 플로이드-워샬 알고리즘을 잘못이해했던 것 같다.<br/>

내가 썼던 방식대로 하면 시간복잡도는 O(N^N)이 되어버리는 것 같다.<br/>

반면에 정석적인 플로이드-워샬 알고리즘은 시간복잡도는 O(N^3)이지.<br/>

그래서 구조체를 제거하고 배열만 써서 정답코드로 바꾸기로 하였다.<br/>

그래서 나온 정답코드가 위의 정답코드이다.<br/>