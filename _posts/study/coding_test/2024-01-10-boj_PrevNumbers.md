---
layout: post
title:  "[백준] 10973_이전 순열 C++"
subtitle:   
date: 2024-01-10 06:56:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10973_이전 순열 C++<br/>
<br/>

순열 조합 구현 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>

using namespace std;

int num[10000] = {0, };
int N;

bool desc(int a, int b)
{
    return a > b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    cin >> N;
    
    for (int i = 0; i < N; ++i)
        cin >> num[i];
    
    bool flag = false;
    for (int i = N - 1; i > 0; --i)
    {
        if(num[i - 1] > num[i])
        {
            int idx = i;
            for (int j = N - 1; j >= i; --j)
            {
                if(num[i - 1] > num[j] && num[j] > num[idx])
                    idx = j;
            }
            
            int temp = num[idx];
            num[idx] = num[i - 1];
            num[i - 1] = temp;
            
            sort(num + i, num + N, desc);
            
            flag = true;
            break;
        }
    }

    if (!flag)
        cout << -1;
    else
        for (int i = 0; i < N; ++i)
            cout << num[i] << ' ';
    
    return 0;
}
```

<br/>

풀이 과정은 아래와 같다.

- 기존 수열의 맨 오른쪽부터 하나씩 왼쪽 원소가 오른쪽 원소보다 큰지 검사한다.
- 조건에 맞으면 그 왼쪽 원소를 중심으로 오른쪽에 존재하는 원소들 중에서 해당 원소보다 작은 값 중 가장 큰 값 을 찾으면 된다.
- 그렇게 찾은 값을 기준의 값과 교환한다.
- 다시 기준을 중심으로 오른쪽 원소들을 내림차순 정렬하면 된다.