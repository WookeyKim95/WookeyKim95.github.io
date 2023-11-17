---
layout: post
title:  "[백준] 14395_4연산 C++"
subtitle:   
date: 2023-11-18 07:22:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14395_4연산 C++<br/>
<br/>

최소 스패닝 트리를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <string>
#include <set>

#define MAX 1000000000

using namespace std;

string BFS(long long S, long long E)
{
    queue<pair<long long, string>> q;
    set<long long> visited;

    q.push({S, ""});
    visited.insert(S);
    char d[4] = {'*', '+', '-', '/'};

    while(!q.empty())
    {
        long long Cur = q.front().first;
        string Accul = q.front().second;
        q.pop();

        if (Cur == 0)
            continue;

        if (Cur == E)
            return Accul;

        for (int i = 0; i < 4; ++i)
        {
            long long Next;
            if (i == 1)
                Next = Cur + Cur;
            else if (i == 2)
                Next = Cur - Cur;
            else if (i == 0)
                Next = Cur * Cur;
            else if (i == 3)
                Next = Cur / Cur;
            
            if (1 <= Next && Next <= MAX && visited.find(Next) == visited.end())
            {
                q.push({Next, Accul + d[i]});
                visited.insert(Next);
            }
                
        }
    }

    return "IMP";
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    long long S, E;
    cin >> S >> E;

    if (S == E)
    {
        cout << 0;
        return 0;
    }

    string res = BFS(S, E);

    if (res == "IMP")
        cout << -1;
    else
        cout << res;
    return 0;
}
```
<br/>
<br/>


## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <string>

using namespace std;

string BFS(int S, int E)
{
    queue<pair<int, string>> q;
    q.push({S, ""});

    char d[4] = {'*', '+', '-', '/'};

    while(!q.empty())
    {
        int Cur = q.front().first;
        string Accul = q.front().second;
        q.pop();

        if (Cur == 0)
            continue;

        if (Cur == E)
            return Accul;
        
        if (Cur > E && !(E == 1 || E % 2 == 0))
            return "IMP";

        for (int i = 0; i < 4; ++i)
        {
            int Next;
            if (i == 1)
                Next = Cur + Cur;
            else if (i == 2)
                Next = Cur - Cur;
            else if (i == 0)
                Next = Cur * Cur;
            else if (i == 3)
                Next = Cur / Cur;
            
            q.push({Next, Accul + d[i]});
        }
    }

    return "IMP";
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int S, E;
    cin >> S >> E;

    if (S == E)
    {
        cout << 0;
        return 0;
    }

    string res = BFS(S, E);

    if (res == "IMP")
        cout << -1;
    else
        cout << res;
    return 0;
}
```

여러모로 생각해볼게 많은 문제였다.<br/>

최소로 계산했을 경우를 구하는 것이기 때문에 BFS를 사용했다.<br/>

그리고 문제에 나와있는 주의할 점은 아스키코드 값이다.<br/>

아스키 코드 순서를 지켜줘야 문제에서 요구하는 순서대로 출력되기 때문에<br/>

맞춰주느라고 아래와 같이 작성했다.<br/>

```
for (int i = 0; i < 4; ++i)
{
    int Next;
    if (i == 1)
        Next = Cur + Cur;
    else if (i == 2)
        Next = Cur - Cur;
    else if (i == 0)
        Next = Cur * Cur;
    else if (i == 3)
        Next = Cur / Cur;
    
    q.push({Next, Accul + d[i]});
}
```

고생했던 부분은 Cur > E 이면 어떻게 처리하는가였다.<br/>

계속해서 더해버리면 무한루프가 발생할 수 있기 때문에<br/>

적절하게 걸러내버리는 작업이 필요했다.<br/>

그리고 이부분.<br/>

```
if (Cur > E && !(E == 1 || E % 2 == 0))
    return "IMP";
```

바로 return을 해버리면 가능성이 있는 부분까지 다 살펴볼 수 없어서 continue로 처리하려했는데<br/>

그렇게 되면 무한루프가 발생해서 어떻게 해야하나 생각했다.<br/>

visited를 쓴다고 해도 최대 범위가 10억이라서 메모리가 터질 것 같았다.<br/>

아! 실마리를 얻었다.<br/>

visited 처리를 set을 이용해서 하면 되는 것이었다.<br/>

set에 들어있냐 아니냐에 따라서 방문여부를 판단하면 메모리가 안터진다!<br/>

이렇게 해서 정답제출코드를 출력하였다.<br/>