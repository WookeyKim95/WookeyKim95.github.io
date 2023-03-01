---
layout: post
title:  "[프로그래머스] 여행경로 C++"
subtitle:   
date: 2023-03-01 09:22:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 여행경로 C++<br/>
<br/>

DFS를 사용하는 그래프 탐색 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```

#include <string>
#include <vector>
#include <algorithm>
#include <iostream>

using namespace std;

vector<string> route;
vector<vector<string>> tickets_static;
int tickets_count;
bool used[10001] = {false};
bool ans = false;

void search(string start, int counter)
{
    route.push_back(start);

    if (counter == tickets_count)
        ans = true;
    
    for (int i = 0; i < tickets_count; ++i)
    {
        // 출발지가 일치하는, 사용하지 않은 티켓을 찾았을 경우
        if (!used[i] && tickets_static[i][0] == start)
        {
            used[i] = true;

            search(tickets_static[i][1], counter+1);
            
            if (!ans)
            {
                used[i] = false;
                route.pop_back();
            }
        }
    }
}

vector<string> solution(vector<vector<string>> tickets) {

    sort(tickets.begin(), tickets.end());
    tickets_static = tickets;
    tickets_count = tickets_static.size();

    search("ICN", 0);

    vector<string> answer = route;
    return answer;
}

```

굳이 struct를 형성하지 않아도 되었다.<br/>

DFS를 이용하고, 시작점과 방문여부를 활용하면 풀 수 있는 문제였던 것같다. 삽질했다..<br/>

아래 오답제출코드는 BFS기반으로 만들어졌다.<br/>

4시간 걸려서도 풀리지가 않아서 결국 구글링을 했고, 코드를 모두 엎었다...<br/>

참고링크는 아래쪽에 있다.<br/>

## 오답제출코드<br/>
<Br/>

```
#include <string>
#include <vector>
#include <algorithm>
#include <iostream>
#include <queue>

using namespace std;

struct Airport
{
    string name;
    queue<string> dests;
};

vector<Airport> Airports;
vector<string> route;

// 공항 목록에 이미 해당 공항이 있는가?
Airport* IsExact(string AirportName)
{
    int len = Airports.size();
    for (int i = 0; i < len; ++i)
    {
        if (Airports[i].name == AirportName)
            return &Airports[i];
    }
    return nullptr;
}

void search(Airport* CurrentPoint)
{
    route.push_back(CurrentPoint->name);

    while(!CurrentPoint->dests.empty())
    {
        Airport* NextPoint = IsExact(CurrentPoint->dests.front());
        
        // 검증용
        // cout << CurrentPoint->name << ' ' << NextPoint->name << '\n';

        CurrentPoint->dests.pop();
        search(NextPoint);
    }
}

vector<string> solution(vector<vector<string>> tickets) {

    int len = tickets.size();

    // 알파벳 순서대로 방문을 하기 위해 정렬
    sort(tickets.begin(), tickets.end());

    for (int i = 0; i < len; ++i)
    {
        Airport* AirportPointer = IsExact(tickets[i][0]);
        if (AirportPointer == nullptr)
        {
            // 공항 목록에 해당 공항이 없을 경우
            // 새로운 공항 생성
            Airport _input;
            _input.name = tickets[i][0];
            
            // 반대편 도착지랑 연결 설정
            _input.dests.push(tickets[i][1]);

            // 공항 목록에 추가
            Airports.push_back(_input);

            // 검증용
            // cout << _input.name << '\n';
        }
        else
        {
            // 공항 목록에 해당 공항이 있을 경우
            // 연결 설정
            AirportPointer->dests.push(tickets[i][1]);
        }
        
        // 티켓 상 도착지에만 존재하는 경우도 방어를 해야함.
        if (IsExact(tickets[i][1]) == nullptr)
        {
            Airport _input;
            _input.name = tickets[i][1];
            Airports.push_back(_input);

            // 검증용
            // cout << _input.name << '\n';
        }
    }
    
    search(IsExact("ICN"));

    vector<string> answer = route;
    return answer;
}
```

반례 : (ICN->BOO) / (ICN->COO) / (COO->ICN)

반례를 찾기가 힘들어서 결국 구글링을 했다.<br/>

[참고](https://nanyoungkim.tistory.com/93)[링크](https://gurumee92.tistory.com/165) 덕분에 반례를 찾을 수 있었다.<br/>

알파벳 순서대로 방문시키기 위해서 큐와 정렬을 사용했는데 그러면 안됐던 것 같다.<br/>

큐를 사용하게 되면 곧 바로 BFS를 사용하게 되고,<br/>

BFS를 사용하게 되면 편도 티켓만 있을 경우 돌아올 수 있는 경로가 없으니 이를 주의했어야 했다.<br/>

알파벳 순서대로 방문을 시키기 위해서 고민을 많이했다.<br/>

