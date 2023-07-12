---
layout: post
title:  "[백준] 1695_팰린드롬 만들기 C++"
subtitle:   
date: 2023-07-02 07:42:15 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1695_팰린드롬 만들기 C++<br/>
<br/>

해시를 쓰는 줄 알았더니 DP를 이용해야 했던 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <cstring>

using namespace std;

int N, ans = 0;
vector<int> arr;
int DP[5001][5001];

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    memset(DP, 0, sizeof(DP));

    cin >> N;
    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        arr.push_back(_input);
    }

    for (int i = 1; i <= N; ++i)
    {
        for (int j = 1; j <= N; ++j)
        {
            if (arr[N-i] == arr[j-1])
                DP[i][j] = DP[i-1][j-1] + 1;
            else
                DP[i][j] = m_max(DP[i][j-1], DP[i-1][j]);
        }
    }

    cout << N-DP[N][N];

    return 0;
}
```

[참고링크](https://ddiyeon.tistory.com/67)를 참고해서 풀었다.<br/>

<br/>

맨 처음 시도했던 방법은 Map을 이용해서 해시를 만드는 것이었다.<br/>

그래서 숫자가 몇개가 저장되어있는지 측정을 하려고 했는데<br/>

문제점이 한두가지가 아니었다.<br/>

대표적으로 수들이 추가되고 난 뒤에 정 가운데 인덱스를 어떻게 구할 것인가?<br/>

그래서 이 방법이 아니라고 생각을 했지만, 도저히 방법이 떠오르지 않아<br/>

검색을 해보기로 했다.<br/>

이런, 이번 문제도 다이나믹 프로그래밍 문제였다.<br/>

아직 DP가 많이 약해서 이 부분을 많이 연습해야겠단 생각이 들었다.<br/>

<br/>

LCS 문제에 적용하는 알고리즘을 이용해서 풀어야 했던 문제라고한다.<br/>

위 링크에 따르면 수열과 역순으로 한 수열의 최장공통수열을 찾아내서 풀면 되는 문제.<br/>

아하! 그러니까 이런 말이다.

- 원래 수열과 역순으로 한 수열의 최장공통수열 길이를 구한다.
- N에서 그 길이 값을 빼면 추가할 수의 개수가 나온다.

그랬던 것이다. 이런 생각을 어떻게 할 수 있는 것일까..??<br/>

LCS구하는 DP 문제도 많이 찾아봐야겠다.<br/>

아직 DP가 전체적으로 많이 부족하니까.<br/>