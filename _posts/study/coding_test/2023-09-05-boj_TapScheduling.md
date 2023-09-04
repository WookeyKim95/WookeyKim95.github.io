---
layout: post
title:  "[백준] 1700_멀티탭 스케줄링 C++"
subtitle:   
date: 2023-09-05 06:58:01 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1700_멀티탭 스케줄링 C++<br/>
<br/>

오랜만에 풀어본 그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <set>
#include <vector>

using namespace std;

int N, M;
set<int> Tab;
vector<int> Schedule;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;
    
    for (int i = 0; i < M; ++i)
    {
        int _input;
        cin >> _input;

        Schedule.push_back(_input);
    }

    int ans = 0;

    for (int i = 0; i < M; ++i)
    {
        int Cur = Schedule[i];

        if (Tab.size() < N)
        {
            Tab.insert(Cur);
            continue;
        }

        if (Tab.find(Cur) != Tab.end())
            continue;
        
        int LastIdx = -1;
        auto ToPop = Tab.begin();
        for (auto iter = Tab.begin(); iter != Tab.end(); ++iter)
        {
            int CurIdx = 0;
            int Can = *iter;
            bool Found = false;

            for (int j = i+1; j < M; ++j)
            {
                if (Schedule[j] == Can)
                {
                    Found = true;
                    CurIdx = j;
                    break;
                }
            }

            if (!Found)
                CurIdx = M;

            if (LastIdx < CurIdx)
            {
                LastIdx = CurIdx;
                ToPop = iter;
            }
                
        }

        Tab.erase(ToPop);
        Tab.insert(Cur);

        ans++;
    }

    cout << ans;


    return 0;
}
```

오랜만에 풀어본 그리디 알고리즘 문제였다.<br/>

우선, 스케줄링을 모두 받아주고<br/>

아래와 같은 상황을 고려해야 했다.<br/>

- 이미 플러그에 꽂혀있는 경우
- 빈 플러그 구멍이 있을 경우
- 빈 플러그 구멍이 없고 이미 꽂혀있지 않은 경우

음.. 이미 플러그에 꽂혀 있는 경우랑 빈 플러그 구멍이 있을 경우는<br/>

그냥 꽂아둔 상태로 두고 continue를 하면 되었기에 괜찮았다.<br/>

문제는 빈 플러그 구멍이 없을 경우 플러그 하나를 빼야하는데<br/>

어떤 기준으로 어떤 플러그를 빼야할지 정하는 것이 문제였다.<br/>

내 풀이를 요약하자면 가장 나중에 다시 꽂히거나 더 이상 꽂히지 않는 플러그를 빼면 됐다.<br/>

<br/>

우선 멀티탭 관리를 set으로 하였다.<br/>

이미 꽂혀있는가?를 찾을 때에도 탐색시간이 빠르고, 중복이 허용되지 않기에 적절할 자료구조로 보였다.<br/>

그리고 가장 나중에 다시 꽂히는 플러그나 더 이상 꽂히지 않는 플러그를 찾는 코드는<br/>

아래와 같이 구현하였다.<br/>

```
int LastIdx = -1;
auto ToPop = Tab.begin();
for (auto iter = Tab.begin(); iter != Tab.end(); ++iter)
{
    int CurIdx = 0;
    int Can = *iter;
    bool Found = false;

    for (int j = i+1; j < M; ++j)
    {
        if (Schedule[j] == Can)
        {
            Found = true;
            CurIdx = j;
            break;
        }
    }

    if (!Found)
        CurIdx = M;

    if (LastIdx < CurIdx)
    {
        LastIdx = CurIdx;
        ToPop = iter;
    }
        
}
```

CurIdx : 이번에 탐색하는 플러그의 나중에 다시 꽂히는 순서
LastIdx : 이전까지 탐색한 것중 가장 나중에 다시 꽂히는 순서
ToPop : 뽑아줄 플러그의 탐색자 (Set 이므로)

그리고 ToPop이 결정되면 뽑아주고 다시 다른 플러그를 넣어준 다음<br/>

ans++를 진행하여 정답을 출력하였다.<br/>