---
layout: post
title:  "[백준] 14719_빗물 C++"
subtitle:   
date: 2023-12-21 06:54:48 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 14719_빗물 C++<br/>
<br/>

시뮬레이션 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int main() {
    int N, M;
    int answer = 0;

    cin >> N >> M;
    vector<int> v(M);

    for (int i = 0; i < M; ++i)
        cin >> v[i];

    for (int i = 1; i < M - 1; ++i)
    {
        int left = 0;
        int right = 0;
        
        for (int j = 0; j < i; ++j)
            left = m_max(left, v[j]);
        
        for (int j = M - 1; j > i; --j)
            right = m_max(right, v[j]);
        
        answer += m_max(0, m_min(left, right) - v[i]);
    }

    cout << answer;
    return 0;
}
```

자신의 현재위치를 기준으로 오른쪽방향으로 젤 큰값과 왼쪽 방향으로 젤 큰 값을 구한다.<br/>

그리고 둘 중 작은 값에다가 자기 자신을 뺀 값을 구하면 한 줄에 채울 수 있는 물의 양이 나온다.<br/>

즉, 이 문제는 한 열에 채울 수 있는 물의 양을 구하고 그것을 모두 합하는 방식으로 구하면 되는 것이다.<br/>