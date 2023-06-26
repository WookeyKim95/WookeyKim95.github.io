---
layout: post
title:  "[백준] 9466_텀 프로젝트 C++"
subtitle:   
date: 2023-06-27 08:05:50 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 9466_텀 프로젝트 C++<br/>
<br/>

DFS를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

struct Student
{
    int Prev;
    int Next;
    bool visited = false;
    bool cycle = false;
};

int N, ans;
vector<Student> Students;

void DFS(int a)
{
    Students[a].visited = true;
    int b = Students[a].Next;

    if (!Students[b].visited)
        DFS(b);

    else if (!Students[b].cycle)
    {
        for (int i = b; i != a; i = Students[i].Next)
            ans++;

		ans++;
    }
    Students[a].cycle = true;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int T;
    cin >> T;

    for (int i = 0; i < T; ++i)
    {
        cin >> N;
        Students.clear();
        ans = 0;

        for (int j = 0; j <= N; ++j)
        {
            Student St;
            Students.push_back(St);
        }

        for (int j = 1; j <= N; ++j)
        {
            int _input;
            cin >> _input;
            Students[j].Next = _input;
            Students[_input].Prev = j;
        }

        for (int j = 1; j <= N; ++j)
        {
            if (!Students[j].visited)
                DFS(j);
        }

        cout << N - ans << '\n';
    }

    return 0;
}
```

<br/>


## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

struct Student
{
    int num;
    int Next;
    bool visited = false;
    bool cycle = false;
};

int N;
vector<Student> Students;

void DFS(int a)
{
    int b = Students[a].Next;
    if (!Students[b].visited)
    {
        Students[b].visited = true;
        DFS(b);
    }
    else
    {
        if (!Students[b].cycle)
        {
            Students[b].cycle = true;
            DFS(b);
        }
            
        else
            return;
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int T;
    cin >> T;

    for (int i = 0; i < T; ++i)
    {
        cin >> N;
        Students.clear();

        for (int j = 0; j <= N; ++j)
        {
            Student St;
            Students.push_back(St);
        }

        for (int j = 1; j <= N; ++j)
        {
            int _input;
            cin >> _input;
            Students[j].Next = _input;
        }

        for (int j = 1; j <= N; ++j)
        {
            if (!Students[j].visited)
                DFS(j);
        }

        int ans = 0;
        for (int j = 1; j <= N; ++j)
        {
            if (!Students[j].cycle)
                ans++;
        }

        cout << ans << '\n';
    }

    return 0;
}
```
<br/>

문제를 보고나서 위와 같은 코드를 생각했는데,<br/>

시간초과가 나지 않을까 걱정했지만 다행히 제한시간이 3초인 것을 보았다.<br/>

그래서 비효율적으로 느껴지더라도 위 방법을 사용하기로 하였다.<br/>

방문하지 않은 모든 인덱스를 시작점으로 지정한다.<br/>

DFS로 방문을 해주면서 방문을 했다고 체크를 해주자.<br/>

그리고 사이클을 돌게되면서 다시 방문지점을 방문을 하게 될 것 이다.<br/>

이 때, 두번째 방문을 한 지점에 대해서는 cycle 변수를 true로 지정해준다.<br/>

즉, 사이클을 두 바퀴 도는 것이다.<br/>

방문을 두 번한 지점은 자연스럽게 사이클을 이루게 될 것이고,<br/>

방문을 한 번만 한 지점은 사이클에 달린 가지와 같은 위치에 있는 곳일 것이다.<br/>

즉, 사이클을 못 이루는 노드라고 보면 된다.<br/>

따라서, DFS를 실행해준 뒤, 방문을 한 번만 한 지점의 개수를 세어주는 식으로 구현하였다.<br/>
<br/>


### 위 코드에 대한 반례<br/>
<br/>

```
1
5
1 1 2 2 3

// 원하는 답 : 4
// 출력된 답 : 3
```

이 테스트 케이스는 그래프가 Y자가 되어있을 때를 나타낸다.<br/>

여기서 2를 원하는 사람이 2명이 있는데, 사이클을 이루지 않는 상태지만 2가 두번 방문받았기 때문에<br/>

사이클을 이루는 형태로 간주되어 1, 2가 사이클을 이루어서 답이 3이 출려되어버리는 일이 일어난 것이다.<br/>
<br/>

### 정답코드로의 변환<br/>
<br/>

어떻게 고쳐야할 지 감이 안잡혀서 결국 구글링을 통해 [참고링크](https://ongveloper.tistory.com/167)를 참고하였다.<br/>

아하! for문을 이렇게도 사용할 수 있구나! 생각이 들었다.<br/>

```
void DFS(int a)
{
    Students[a].visited = true;
    int b = Students[a].Next;

    if (!Students[b].visited)
        DFS(b);

    else if (!Students[b].cycle)
    {
        for (int i = b; i != a; i = Students[i].Next)
            ans++;

		ans++;
    }
    Students[a].cycle = true;
}
```

만약에 방문을 한 노드인데 아직 사이클을 이루지 않는다면<br/>

사이클을 돌면서 ans값을 올리도록 하는 것이다.<br/>

```
cout << N - ans << '\n';
```

그리고 전체 - ans 값을 빼면 그것이 진짜 답이 된다.<br/>

결국, 전체적인 로직 자체는 같았던 것으로 판단된다.<br/>

아.. 네이밍이 살짝 아쉽네. ans가 아니라 cnt라고 했으면 더 좋았을 것을.