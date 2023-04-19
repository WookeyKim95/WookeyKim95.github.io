---
layout: post
title:  "[백준] 1477번_휴게소 세우기 C++"
subtitle:   
date: 2023-04-19 10:08:10 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1477번_휴게소 세우기 C++<br/>
<br/>

이분 탐색을 사용해야 했던 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int N, M, L;
vector<int> RestArea;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M >> L;

    RestArea.push_back(0);
    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        RestArea.push_back(_input);
    }
    RestArea.push_back(L);

    sort(RestArea.begin(), RestArea.end());
    
    int Low = 1, High = L-1;
    int ans = 0, mid = 0;

    while (Low <= High)
    {
        mid = (Low + High) / 2;
        int counter = 0;
        for (size_t i = 1; i < RestArea.size(); ++i)
        {
            int distance = RestArea[i] - RestArea[i-1];

            counter += (distance / mid);
            if (distance % mid == 0)
                counter--;
        }
        
        if (counter > M)
            Low = mid + 1;
        else
        {
            ans = mid;
            High = mid - 1;
        }
    }

    cout << ans;
    return 0;
}
```

이분 탐색이라는 것을 인지하였고, 이분 탐색의 기본적인 틀은 구현하였지만<br/>

이를 어떻게 사용해야 할지를 많이 고민했지만 슬프게도 내 힘으론 못찾았다.<br/>

결국 [참고링크](https://gusdnr69.tistory.com/277)를 참고하여 문제를 풀었다.<br/>

감사하게도 주석을 달아놔주셔서 이해를 쉽게 할 수 있었다.<br/>

우선, 0과 L을 포함한 휴게소의 지점을 모두 벡터에 받아놓는다.<br/>

그리고 이분 탐색을 해주기 위해서 벡터를 정렬한다.<br/>

이 문제의 핵심은 거리를 기반으로 이분탐색을 실시하는 것이었다.<br/>

휴게소 사이의 거리 distance를 조사하고, mid 값으로 distance를 나누어 줌으로써<br/>

휴게소를 몇 개 세울 수 있는가(counter), 그리고 그 지점에 이미 휴게소가 있는가를 조사한다.<br/>

그리고 counter값이 배치할 수 있는 휴개소 M보다 많으면 mid 값을 늘려주기 위해 Low = mid + 1로 갱신<br/>

counter값이 배치할 수 있는 휴개소 M보다 많으면 mid 값을 줄여주기 위해 High = mid - 1로 갱신한다.<br/>

그리고 이 과정에서 ans 값을 갱신한다.<br/>

이렇게 해서 ans값을 구하게 된다.<br/>

이분 탐색을 응용하는 과정은 아직 많이 부족한 것 같다. 더 연습해야지.<br/>