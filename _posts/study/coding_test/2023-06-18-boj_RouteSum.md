---
layout: post
title:  "[백준] 11660_구간 합 구하기 5 C++"
subtitle:   
date: 2023-06-18 10:25:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11660_구간 합 구하기 5 C++<br/>
<br/>

DP를 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int mat[1001][1001];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N, M;
    cin >> N >> M;

    int temp;

    for (int i = 1; i <= N; ++i)
    {
        int sum = 0;
        for (int j = 1; j <= N; ++j)
        {
            cin >> temp;
            mat[i][j] = mat[i-1][j] + mat[i][j-1] + temp - mat[i-1][j-1]; 
        }
    }

    int x1, y1, x2, y2;
    
    for (int i = 0; i < M; ++i)
    {
        int result = 0;
        cin >> x1 >> y1 >> x2 >> y2;
        result = mat[x2][y2] - mat[x1-1][y2] - mat[x2][y1-1] + mat[x1-1][y1-1]; 

        cout << result << '\n';
    }

    return 0;
}
```
<br/>

면접보느라 휴식을 하고 싶지만 스트릭은 채워야 하기 때문에 풀어본 실버문제.<br/>

for문을 돌려서 해결할 수는 없는게 시간초과가 난다. 그래서 DP를 이용해야한다.<br/>

행렬에 있는 값을 입력 받으면서 i행 j열에 있는 값은 그 구간까지의 합으로 정의를 해준다.<br/>

```
cin >> temp;
mat[i][j] = mat[i-1][j] + mat[i][j-1] + temp - mat[i-1][j-1]; 
```

그리고 원하는 구간을 구하고 싶을 때 식을 아래와 같이 계산해준다.<br/>

```
cin >> x1 >> y1 >> x2 >> y2;
result = mat[x2][y2] - mat[x1-1][y2] - mat[x2][y1-1] + mat[x1-1][y1-1];
```