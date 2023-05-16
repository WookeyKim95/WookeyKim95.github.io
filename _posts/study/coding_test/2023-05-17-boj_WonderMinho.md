---
layout: post
title:  "[백준] 1507_궁금한 민호 C++"
subtitle:   
date: 2023-05-17 08:33:29 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1507_궁금한 민호 C++<br/>
<br/>

플로이드-워셜 알고리즘을 응용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>

using namespace std;

int N;
int ans = 0;
vector<vector<int>> v;
bool Connected[20][20];

int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        vector<int> Vinput;
        for (int j = 0; j < N; ++j)
        {
            int _input;
            cin >> _input;
            Vinput.push_back(_input);
        }
        v.push_back(Vinput);
    }

    memset(Connected, true, sizeof(Connected));

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            for (int k = 0; k < N; ++k)
            {
                if (i == j || j == k || k == i)
                    continue;

                // 플로이드-워셜 알고리즘이 불가능하므로 -1을 출력하고 끝.
                else if (v[j][k] > v[j][i] + v[i][k])
                {
                    cout << -1;
                    return 0;
                }

                // 다른 도로를 거쳐가게 되므로 도로가 연결됐는지 여부를 false 처리한다.
                else if (v[j][k] == v[j][i] + v[i][k])
                    Connected[j][k] = false;
            }
        }
    }

    for (int i = 0; i < N; ++i)
    {
        for (int j = 0; j < N; ++j)
        {
            if (Connected[i][j])
                ans += v[i][j];
        }
    }

    cout << ans / 2;

    return 0;
}
```

<br/>

원래 같으면 플로이드-워셜 알고리즘을 사용할 때

전체를 무한대로 초기화하고, 간선 정보를 입력받고 해당 노드까지 거리 구하고<br/>

이런 과정을 통해 결과를 도출하는데<br/>

이번 문제 같은 경우에는 결과를 가지고 간선 정보를 도출하라는 느낌?<br/>

즉 본래 있었던 플로이드-워셜 알고리즘의 역순으로 진행되는 느낌이었다.<br/>

주어진 값으로부터 노드를 어떻게 추출할지 고민하는 과정이 힘들었다.<br/>

기존의 플로이드-워셜 알고리즘에서 간선의 값을 통해서 거리를 추출하는 과정을 보면<br/>

```
for (int i = 0; i < N; ++i)
{
    for (int j = 0; j < N; ++j)
    {
        for (int k = 0; k < N; ++k)
            v[j][k] = min(v[j][k], v[j][i] + v[i][k]);
    }
}
```

대략 이런 느낌이다.<br/>

```
v[j][k] = min(v[j][k], v[j][i] + v[i][k]);
```
을 하고 난 뒤의 값이 입력 값으로 주어지는 값들이란 말이지?<br/>

입력 테스트 케이스 1을 보면 (1-2경로 + 2-3경로) = (1-3)경로이다.<br/>

흠.. 이런 식이 성립하는 것을 통해서 구할 수 있으려나?

그렇네! (1-2경로 + 2-3경로) = (1-3)경로 가 성립하는 것은<br/>

직접적으로 연결된 경로가 아니라 다른 경로를 통해서 온다는 것이니까<br/>

빼버리면 되는 것이다!<br/>

```
if (i == j || j == k || k == i)
    continue;

// 플로이드-워셜 알고리즘이 불가능하므로 -1을 출력하고 끝.
else if (v[j][k] > v[j][i] + v[i][k])
{
    cout << -1;
    return 0;
}

// 다른 도로를 거쳐가게 되므로 도로가 연결됐는지 여부를 false 처리한다.
else if (v[j][k] == v[j][i] + v[i][k])
    Connected[j][k] = false;
```

그리고 플로이드 워셜 알고리즘이 불가능한 경우가 발견되면<br/>

그냥 -1을 출력하고 끝내버린다.<br/>

```
for (int i = 0; i < N; ++i)
{
    for (int j = 0; j < N; ++j)
    {
        if (Connected[i][j])
            ans += v[i][j];
    }
}

cout << ans / 2;

```

마지막으로, 도로가 연결됐는지 여부가 true인 부분만<br/>

ans에 누적시켜준 다음에 2를 나눠준다.<br/>

2를 나눠주는 이유는 대칭행렬이다보니, 값을 두번씩 더해줬기 때문이다.<br/>

그래서 반을 나눠줘야한다.<br/>
