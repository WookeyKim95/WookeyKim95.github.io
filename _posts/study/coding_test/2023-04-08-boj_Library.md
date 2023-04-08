---
layout: post
title:  "[백준] 1461번_도서관 C++"
subtitle:   
date: 2023-04-08 09:13:31 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1461번_도서관 C++<br/>
<br/>

정렬과 그리디 알고리즘을 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>
#include <vector>
#include <cmath>

using namespace std;

int N, M, ans = 0;
vector<int> books;
int zeroIndex = 0;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        books.push_back(_input);
        if (_input < 0)
            zeroIndex++;
    }

    sort(books.begin(), books.end());

    for (int i = N - 1; i >= zeroIndex; i -= M)
        ans += books[i] * 2;
    
    for (int i = 0; i < zeroIndex; i += M)
        ans += abs(books[i] * 2);
    
    ans -= max(abs(books[0]), abs(books[N-1]));

    cout << ans;

    return 0;
}
```

우선, 음수와 양수를 분리해서 옮기기 위해서 zeroIndex를 설정해주었다.<br/>

zeroIndex가 책을 옮길 때 음수와 양수를 분리하는 기준점 역할을 해줄 것이다.<br/>

음수와 양수를 분리하는 이유는 이동거리를 최소화하기 위해서이다.<br/>

음수는 음수끼리, 양수는 양수끼리 옮겨야 이동거리를 최소화할 수 있다.<br/>

<br/>

그리고 정렬을 해준다.<br/>

정렬을 해준 뒤에 양 끝에서부터 M권씩 한꺼번에 책을 옮기도록 하였다.<br/>

이 때, 왕복을 하기 때문에 거리 * 2 만큼 더하도록 하였다.<br/>

그리고 M권씩 옮기기 때문에 for문도 M간격으로 돌도록 하였다.<br/>

이렇게하면 M권을 들고 거리가 가장 먼 책까지 이동하는 과정에서 나머지 책들을 꽂을 수가 있다.<br/>

그리고 마지막에 거리가 가장 먼 책, 즉 절댓값이 가장 큰 책을 마지막으로 옮기는 것이 거리 상에서 가장 유리할 것이다.<br/>

따라서, 거리가 가장 먼 책은 편도로 이동만 하게 하기 위해서 

```
    ans -= max(abs(books[0]), abs(books[N-1]));
```
을 실행해준다.