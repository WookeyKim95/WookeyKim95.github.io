---
layout: post
title:  "[백준] 1449번_수리공 항승 C++"
subtitle:   
date: 2023-02-21 09:23:12 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1449번_수리공 항승 C++<br/>
<br/>

그리디 알고리즘을 활용하는 문제<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
    int N, L;
    cin >> N >> L;

    vector<int> v;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        v.push_back(_input);
    }

    sort(v.begin(), v.end());

    int start = v[0];
    int count = 1;
    
    for (int i = 1; i < N; ++i)
    {
        if (start <= v[i] && v[i] < start+L)
            continue;
        else
        {
            start = v[i];
            count++;
        }
    }
    cout << count;

    return 0;
}
```

입력받은 수도관의 구멍의 위치를 오름차순으로 정렬해준다.<br/>

그리고 첫번째 구멍의 위치를 시작지점으로 지정하고, 필요한 테이프의 개수 count를 1로 지정한다.<br/>

구멍의 위치를 조사하면서 구멍의 위치가 테이프의 start지점으로부터 테이프의 길이 L 내에 있을 경우에는 계속하고,<br/>

테이프의 길이를 벗어났을 경우 새로운 시작점으로 지정하고, 테이프의 길이를 늘리는 식으로 구현하였다.<br/>

