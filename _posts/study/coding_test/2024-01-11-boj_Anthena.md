---
layout: post
title:  "[백준] 18310_안테나 C++"
subtitle:   
date: 2024-01-11 06:57:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 18310_안테나 C++<br/>
<br/>

그리디 알고리즘 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef long long ll;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    vector<int> vec;

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        vec.push_back(_input);
    }

    sort(vec.begin(), vec.end());

    cout << vec[(N-1)/2];

    return 0;
}
```

<br/>

이 문제를 읽고나서 그리디 알고리즘이라고 생각했다.<br/>

문제는, 일일이 자리마다 계산을 해주면 시간초과가 날 것은 뻔했던 것이다.<br/>

그래서 처음에 내가 생각했던 방법은<br/>

map에다가 좌표마다 집의 개수를 기록해주고, 집이 많은 순서대로 정렬해서<br/>

집이 가장 많은 좌표를 우선적으로 계산해주는 방법이었다.<br/>

조금 더 살펴보니 그런데 그럴 필요가 없었다.<br/>

그냥 좌표를 정렬한 뒤, 정 가운데에 있는 좌표가 곧 정답이었던 것이다.<br/>

이게 어떻게 가능할까?<br/>

```
a b c d e
```
이 배열이 있다고 하고, ab, bc, cd, de의 사이의 거리를 각각 x, y, z, t 라고 하자.<br/>

이 때, 각 a, b, c, d, e에 안테나가 위치할 때 거리의 총합은 아래와 같아진다.<br/>

- a: 4x + 3y + 2z + t
- b: x + 3y + 2z + t
- c: x + 2y + 2z + t
- d: x + 2y + 3z + t
- e: x + 2y + 3z + 4t 

이 때, x. y, z, t의 값에 상관 없이 c, 즉 가운데에 위치해야 최소값이 되는 것을 알 수 있다.<br/>

그래서 vec[(N-1)/2]을 구하도록 구현하였다.<br/>