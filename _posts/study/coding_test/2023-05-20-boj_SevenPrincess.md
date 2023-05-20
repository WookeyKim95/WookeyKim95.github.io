---
layout: post
title:  "[백준] 1941_소문난 칠공주 C++"
subtitle:   
date: 2023-05-20 07:40:53 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1941_소문난 칠공주 C++<br/>
<br/>

DFS와 BFS를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <algorithm>
#include <queue>

#define pii pair<int, int>

using namespace std;


vector<string> Map;
int ans = 0;
bool Selected[25];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

bool Check()
{
    int Y_Count = 0;
    for (int i = 0; i < 25; i++)
    {
        if (Selected[i])
        {
            int x = i % 5;
            int y = i / 5;
 
            if (Map[y][x] == 'Y')
                Y_Count++;
        }
    }

    if (Y_Count >= 4)
        return false;
    else
        return true;
}

bool BFS()
{
    queue<pii> q;

    bool Check_Answer = false;

    bool Check_Select[5][5];
    bool Queue_Select[5][5];
 
    memset(Queue_Select, false, sizeof(Queue_Select));
    memset(Check_Select, false, sizeof(Check_Select));
 
    bool flag = false;
    for (int i = 0; i < 25; i++)
    {
        if (Selected[i])
        {            
            int y = i / 5;
            int x = i % 5;
            Check_Select[y][x] = true;
 
            if (!flag)
            {
                q.push(make_pair(x, y));
                Queue_Select[y][x] = true;
                flag = true;
            }
        }
    }

    int Cnt = 1;
    while (!q.empty())
    {
        int x = q.front().first;
        int y = q.front().second;
        q.pop();
 
        if (Cnt == 7)
        {
            Check_Answer = true;
            break;
        }
 
        for (int i = 0; i < 4; i++)
        {
            int nx = x + dx[i];
            int ny = y + dy[i];
 
            if (nx >= 0 && ny >= 0 && nx < 5 && ny < 5)
            {
                if (Check_Select[ny][nx] && !Queue_Select[ny][nx])
                {
                    Queue_Select[ny][nx] = true;
                    q.push({nx, ny});
                    Cnt++;
                }
            }
        }
    }

    if (Check_Answer)
        return true;
    else
        return false;
}

void backtrack(int cnt, int idx)
{
    if (cnt == 7)
    {
        if (Check())
        {
            if (BFS())
                ans++;
        }
        return;
    }

    for (int i = idx; i < 25; ++i)
    {
        if (!Selected[i])
        {
            Selected[i] = true;
            backtrack(cnt+1, i);
            Selected[i] = false;
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    for (int i = 0; i < 5; ++i)
    {
        string _input;
        cin >> _input;

        Map.push_back(_input);
    }

    backtrack(0, 0);
    
    cout << ans;

    return 0;
}
```
<br/>

정답 코드는 [참고링크](https://yabmoons.tistory.com/117)를 참고하였다.<br/>

항상 같은 분의 블로그를 참고하는 것 같다. 감사드립니다.(꾸벅)<br/>

우선 DFS를 이용한 Backtrack기법으로 브루트포스를 진행하고,<br/>

학생들이 인접해서 앉아있는지 여부를 BFS를 통해서 측정하는 것이다.<br/>

- Check_Select[ny][nx] : 내가 DFS를 통해서 뽑은 7명의 학생 중 한명인가?
- Queue_Select[ny][nx] : 이미 큐에서 고른 학생인가?

위 두가지 조건을 따지면서 BFS탐색을 하면되고, 만족하면 카운트를 해준다.<br/>

이렇게 해서 카운트가 7이 되면 그때 BFS 탐색을 끝내고 true를 반환하면 되는 것이다.<br/>


## 초기작성코드<br/>
<br/>

```
#include <iostream>
#include <cstring>
#include <vector>
#include <algorithm>

#define vpp vector<pair<char, pair<int, int>>>

using namespace std;

vector<vpp> Already;
vector<string> Map;
vpp line;
int ans = 0;
bool visited[5][5];

int dx[4] = {-1, 1, 0, 0};
int dy[4] = {0, 0, -1, 1};

bool Check()
{
    int Y_Count = 0;
    for (size_t i = 0; i < line.size(); ++i)
    {
        if (line[i].first == 'Y')
            Y_Count++;
    }

    if (Y_Count >= 4)
        return false;
    else
        return true;
}

bool NotAlready()
{
    vpp temp = line;
    sort(temp.begin(), temp.end());

    for (size_t i = 0; i < Already.size(); ++i)
    {
        if (Already[i] == temp)
            return false;
    }
    Already.push_back(temp);
    return true;
}

void backtrack(int cnt, int x, int y)
{
    if (cnt == 7)
    {
        if (Check())
        {
            if (NotAlready())
                ans++;
        }
        return;
    }

    for (int i = 0; i < 4; ++i)
    {
        int nx = x + dx[i];
        int ny = y + dy[i];

        if (0 <= nx && nx < 5 && 0 <= ny && ny < 5 && !visited[ny][nx])
        {
            visited[ny][nx] = true;
            line.push_back({Map[ny][nx], {nx, ny}});
            backtrack(cnt+1, nx, ny);
            line.pop_back();
            visited[ny][nx] = false;
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    for (int i = 0; i < 5; ++i)
    {
        string _input;
        cin >> _input;

        Map.push_back(_input);
    }

    for (int i = 0; i < 5; ++i)
    {
        for (int j = 0; j < 5; ++j)
        {
            visited[i][j] = true;
            line.push_back({Map[i][j], {j, i}});
            backtrack(1, j, i);
            line.pop_back();
            visited[i][j] = false;
        }
    }
    
    cout << ans;

    return 0;
}
```

<br/>

백트래킹을 위한 브루트포스 알고리즘을 생각해서 DFS형식으로 구현하였다.<br/>

DFS를 통해서 문자와 좌표값을 line벡터에 쌓은 다음에<br/>

중복을 방지하기 위해서 line 벡터를 임시로 받은 temp를 정렬한 다음에 중복 여부를 체크했다.<br/>

그래서 중복이 안된 것이라면 Already에 새로 넣고,<br/>

그렇지 않다면 중복이 된 것이므로 패스를 하도록 구현하였다.<br/>

하지만 DFS로 탐색하니 문제가 발생했다.<br/>

바로 ㅜ자 형 경로는 탐색이 안된다는 것이다.<br/>

DFS는 꼬리에 꼬리를 무는 형식이기 때문에 ㄱ자 경로는 탐색이 되지만 ㅜ자형은 탐색이 되지 않는다.<br/>

그래서 탐색하는 부분은 BFS로 구현할 필요가 있을 것 같았다.<br/>