---
layout: post
title:  "[백준] 1300_K번째 수 C++"
subtitle:   
date: 2023-06-01 08:58:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1300_K번째 수 C++<br/>
<br/>

알고보니 이분탐색을 활용 하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

typedef long long ll;

ll N, K;

ll m_min(ll a, ll b)
{
    if (a < b)
        return a;
    else
        return b;
}

ll LessNumMatrix(ll mid){
    ll cnt = 0;

    for(ll i = 1; i <= N; i++)
    {
        cnt += m_min(N, mid / i);

        if (i > mid)
            break;
    }
    
    return cnt;
}

int main()
{
    cin >> N;
    cin >> K;

    ll Low = 1;
    ll High = K;
    
    ll res = 0;

    while (Low <= High)
    {
        ll mid = (Low + High) / 2;

        if (LessNumMatrix(mid) < K)
            Low = mid + 1;
        else
        {
            res = mid;
            High = mid - 1;
        }
    }

    cout << res;

    return 0;
}
```
<br/>

문제를 읽자마자 드는 생각은 이게 뭔소리지? 였다.<br/>

골드급인 이유가 있을 것이다 생각을 하면서 코드를 짜봤는데<br/>

아하.. 이유가 역시 있었다.<br/>

```
#define MAX 100000

int Mat[MAX+1][MAX+1];
```

이렇게 짜려고하면 메모리가 너무 커서 배열 선언자체가 되지 않는다.<br/>

할 수 없이 이 부분은 벡터로 구현하기로 하였다.<br/>

근데 우선 초반에 구현한 부분이다.<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

#define MAX 100000

using namespace std;

int N, K;
vector<vector<int>> A;
vector<int> B;

int main()
{
    cin >> N;

    for (int i = 1; i <= N; ++i)
    {
        vector<int> row;
        for (int j = 1; j <= N; ++j)
        {
            row.push_back(i*j);
            B.push_back(i*j);
        }
        
        A.push_back(row);
    }

    sort(B.begin(), B.end());

    cin >> K;
    cout << B[K-1];

    return 0;
}
```

이렇게 하면 예시는 잘 나온다 물론...<br/>

하지만 큰 수가 들어간다면?<br/>

```
terminate called after throwing an instance of 'std::bad_alloc'
  what():  std::bad_alloc
```

그렇다. 메모리 초과다.<br/>

아하 그래서 난이도가 높은 문제로 책정이 됐구나!<br/>

아이디어가 떠오르지 않아서 구글링을 하고 [참고링크](https://hyeo-noo.tistory.com/64)를 참고하였다.<br/>

알고보니 접근 방법이 달랐다.<br/>

이분탐색으로 접근을 해야하는 문제였던 것이다.<br/>

우선, 이분 탐색을 할 타겟의 종류는 값으로 설정해준다.<br/>

그래서 Low = 0, High = N*N에서 출발한다.<br/>

그 다음에 (Low + High) / 2의 값이 몇 행, 몇 열에 있는지 출력을 하고<br/>

(Low + High) / 2의 값보다 작은 값의 개수가 K보다 작으면 더 위쪽을 탐색,<br/>

그렇지 않다면 더 아래쪽을 탐색하여 K를 찾아간다.<br/>

그렇게 해서 K의 지점의 행과 열을 구해서 답을 출력하는 시스템으로 구현하였다.<br/>
