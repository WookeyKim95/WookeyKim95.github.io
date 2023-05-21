---
layout: post
title:  "[백준] 1963_소수 경로 C++"
subtitle:   
date: 2023-05-21 12:55:40 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1963_소수 경로 C++<br/>
<br/>

에라토스테네스의 체와 BFS를 응용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <cmath>
#include <queue>
#include <string>

#define MAX 10000

using namespace std;

bool IsPrime[MAX];
bool visited[MAX];

void Eratosthenes()
{
    memset(IsPrime, true, sizeof(IsPrime));
    IsPrime[2] = true;
    IsPrime[3] = true;

    for (int i = 2; i <= sqrt(MAX); i++)
    {
        if (!IsPrime[i])
            continue;
        for (int j = i * 2; j <= MAX; j += i)
            IsPrime[j] = false;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int TC;
    cin >> TC;

    Eratosthenes();

    for (int i = 0; i < TC; ++i)
    {
        int Start, End;
        cin >> Start >> End;

        bool flag = false;
        memset(visited, false, sizeof(visited));
        
        queue<pair<int, int>> q;
        q.push({Start, 0});
        visited[Start] = true;

        while (!q.empty())
        {
            int CurNum = q.front().first;
            int Cnt = q.front().second;
            q.pop();

            if (CurNum == End)
            {
                cout << Cnt << '\n';
                flag = true;
                break;
            }

            for (int i = 0; i < 4; ++i)
            {
                int NextNum;
                for (int j = 0; j < 10; j++)
                {
                    string s = to_string(CurNum);
                    s[i] = j + '0';
                    NextNum = stoi(s);
    
                    if (!IsPrime[NextNum] || visited[NextNum] || (NextNum >= 10000 || NextNum < 1000))
                        continue;
    
                    visited[NextNum] = true;
                    q.push(make_pair(NextNum, Cnt + 1));                
                }
            }
        }

        if (!flag)
            cout << "Impossible" << '\n';
    }

    return 0;
}
```
<br/>

아이디어가 떠오르지 않아서 알고리즘 분류를 보았더니<br/>

에라토스테네스의 체인 것은 알았는데 BFS를 쓰리라곤 생각도 못했다.<br/>

여기서 BFS를 어떻게 쓴다는 것일까? 곰곰히 생각을 했다.<br/>

검색을 해보니 현재 수에서 다음 수로 바꾸는 과정을 BFS로 탐색을 해야하는 것이었다.<br/>

```
for (int i = 0; i < 4; ++i)
{
    int NextNum;
    for (int j = 0; j < 10; j++)
    {
        string s = to_string(CurNum);
        s[i] = j + '0';
        NextNum = stoi(s);

        if (!IsPrime[NextNum] || visited[NextNum] || (NextNum >= 10000 || NextNum < 1000))
            continue;

        visited[NextNum] = true;
        q.push(make_pair(NextNum, Cnt + 1));                
    }
}
```

위 코드는 [참고링크](https://yabmoons.tistory.com/65)를 참고하였다.<br/>

이해를 해보니, 한 회에 숫자 하나만 바꿀 수 있는 것이므로<br/>

for문을 돌려서 각 자리를 1~9로 바꿔준 다음에 소수인지 여부를 판별하는 것으로<br/>

구현하는 것이었다.<br/>

만약에 조건을 만족한다면 큐에 교환 횟수와 함께 쌍으로 넣어주는 것으로 구현한다.<br/>

이렇게 해서 조건에 맞는 숫자가 나오면 flag를 true로 반환, 그렇지 않으면 false를 유지하여

Impossible을 출력하도록 만든다.<br/>