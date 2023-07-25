---
layout: post
title:  "[백준] 2109_순회강연 C++"
subtitle:   
date: 2023-07-26 07:33:58 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2109_순회강연 C++<br/>
<br/>

우선순위 큐를 활용하는 그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>
#include <vector>
#include <algorithm>

using namespace std;

typedef pair<int, int> pii;

priority_queue<int, vector<int>, greater<int>> pq;
int N;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    vector<pii> lectures;
    for (int i = 0; i < N; ++i)
    {
        int price, day;
        cin >> price >> day;

        lectures.push_back({day, price});
    }

    sort(lectures.begin(), lectures.end());

    int ans = 0;
    for (size_t i = 0; i < lectures.size(); ++i)
    {
        ans += lectures[i].second;
        pq.push(lectures[i].second);

        if (lectures[i].first < pq.size())
        {
            ans -= pq.top();
            pq.pop();
        }
    }

    cout << ans;

    return 0;
}
```


우선순위 큐의 정렬 순서를 정하고 이를 어떻게 활용하는 가가 문제였다.<br/>

그리고 며칠 안에 와야 한다는 것을 어떻게 구현하면 좋을지 고민하였다.<br/>

생각보다는 간단했다.<br/>

vector에다가 입력을 받고 day순으로 정렬해준 뒤에 가격을 우선순위 큐에 넣어준다.<br/>

이 때 우선순위 큐는 낮은 숫자가 제일 먼저오게 정렬을 해준다.<br/>

그리고 우선순위 큐의 길이가 날짜를 넘어가게 되면 작은 것부터 빼주면 되는 것이었다.<br/>
<br/>

## 오답제출코드<br/>
<Br/>

```
#include <iostream>
#include <queue>
#include <vector>

using namespace std;

typedef pair<int, int> pii;

struct cmp
{ 
    bool operator()(pii a, pii b)
    {
        if (a.second == b.second)
            return a.first < b.first;
        else
            return a.second > b.second;
    }
};

priority_queue<pii, vector<pii>, cmp> pq;
int N;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    for (int i = 0; i < N; ++i)
    {
        int price, day;
        cin >> price >> day;

        pq.push({price, day});
    }

    int curDay = 1;
    int ans = 0;
    bool flag = true;

    while (!pq.empty())
    {
        pii Top = pq.top();

        if (curDay == Top.second && flag)
        {
            ans += Top.first;
            flag = false;
            curDay++;
        }

        else if (curDay == Top.second && !flag)
        {
            flag = true;
            continue;
        }

        else if (curDay < Top.second && !flag)
        {
            curDay = Top.second;
            continue;
        }

        pq.pop();
    }

    cout << ans;

    return 0;
}
```


우선, 우선순위 큐의 정렬 순서를 구현하는 부분은 아래와 같다.<br/>

```
typedef pair<int, int> pii;

struct cmp
{ 
    bool operator()(pii a, pii b)
    {
        if (a.second == b.second)
            return a.first < b.first;
        else
            return a.second > b.second;
    }
};
```

가격 정보는 first 부분에, 날짜 정보는 second 부분에 위치해있다.<br/>

따라서 내가 의도한 부분은 아래와 같다.<br/>

- 날짜가 빠른 순서대로 정렬하기
- 날짜가 같으면 높은 가격 순서대로 정렬하기

이런 순서대로 정렬되도록 구현하였다.<br/>

그리고 우선순위 큐가 작동되는 부분은 아래와 같다.<br/>

```
int curDay = 1;
int ans = 0;
bool flag = true;

while (!pq.empty())
{
    pii Top = pq.top();

    if (curDay == Top.second && flag)
    {
        ans += Top.first;
        flag = false;
        curDay++;
    }

    else if (curDay == Top.second && !flag)
    {
        flag = true;
        continue;
    }

    else if (curDay < Top.second && !flag)
    {
        curDay = Top.second;
        continue;
    }

    pq.pop();
}
```

의도한 우선순위 큐의 작동은 아래와 같다.

- 현재 날짜에서 가장 높은 금액일 경우, ans에 , 그리고 큐에서 제거
- 현재 날짜에서 가장 높은 금액이 아닐 경우, pass하고 큐에서 제거
- top에 있는 날짜보다 현재 날짜가 낮을 경우, 현재 날짜를 top에 있는 날짜에 맞추기
- 현재 날짜인데 flag가 false일 경우, flag를 true로 전환

flag는 가장 높은 금액인가? 라는 여부를 나타내주는 bool 변수이다.<br/>

이렇게 구현하였다.<br/>

그런데 오답이라고 출력이되었네?<br/>

내가 착각한 부분이 한가지 있다.<br/>

며칠 안에 와야한다는 표현을 며칠 째에 가야한다는 식으로 구현해버린 것이다.<br/>

그래서 이 부분을 수정할 필요가 있었다.<br/>

그렇다. 결론은 코드를 엎었다. ㅋㅋ<br/>

그래서 나온 코드가 위의 정답코드이다.<br/>