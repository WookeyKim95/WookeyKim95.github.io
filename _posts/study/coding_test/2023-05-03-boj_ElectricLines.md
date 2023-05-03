---
layout: post
title:  "[백준] 2565_전깃줄 C++"
subtitle:   
date: 2023-05-03 10:26:06 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2565_전깃줄 C++<br/>
<br/>

간단한 정렬과 DP를 사용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int DP[101];

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

    vector<pair<int, int>> lines;

    int N;
    cin >> N;

    pair<int, int> padding = {0, 0};
    lines.push_back(padding);

    for (int i = 1; i <= N; ++i)
    {
        int start, end;
        cin >> start >> end;

        lines.push_back({start, end});
    }

    int AscLines = 0;

    sort(lines.begin()+1, lines.end());
    for (int i = 1; i <= N; ++i)
    {
        DP[i] = 1;
        for (int j = 1; j < i; ++j)
        {
            if (lines[i].second > lines[j].second)
                DP[i] = m_max(DP[i], DP[j] + 1);
        }
        AscLines = m_max(DP[i], AscLines);
    }

    cout << N - AscLines;

    return 0;
}
```

사실 내가 생각한 풀이는 이랬다.<br/>

왼쪽 값을 기준으로 오름차순 정렬을 시키고 양 쪽이 모두 최장증가수열인 경우<br/>

그 때의 수열의 길이가 남겨둘 수 있는 전깃줄의 개수이다.<br/>

예를 들어 문제에서 나온 예시를 시작점을 기준(첫번째 열을 기준)으로 오름차순 정렬하면<br/>

```
정렬 전    정렬 후
1 8        1 8
3 9        2 2
2 2        3 9
4 1        4 1
6 4        6 4
10 10      7 6
9 7        9 7
7 6        10 10
```

위와 같아진다.<br>

여기서 첫번째 열과 두 번째 열이 모두 오름차순인 최대 길이를 가지는 경우는<br/>

```
4 1
6 4
7 6
9 7
10 10
```
위의 경우이며, 제외된 나머지의 개수는 3개이다.<br/>

즉, 3개를 제외하면 나머지 전선은 교차하지 않게 되는 것이다.<br/>

이렇게 구현을 하고 싶었는데 어떻게 구현을 할까 살펴보니<br/>

구글링을 해보니 DP를 사용했더라.<br/>

```
    for (int i = 1; i <= N; ++i)
    {
        DP[i] = 1;
        for (int j = 1; j < i; ++j)
        {
            if (lines[i].second > lines[j].second)
                DP[i] = m_max(DP[i], DP[j] + 1);
        }
        AscLines = m_max(DP[i], AscLines);
    }
```

양쪽이 모두 오름차순인 가장 긴 부분순열의 길이를 구하는 과정이다.<br/>

이렇게 구현하여 정답을 도출하였다.<br/>