---
layout: post
title:  "[백준] 10942_팰린드롬? C++"
subtitle:   
date: 2023-06-28 08:19:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10942_팰린드롬? C++<br/>
<br/>

DP를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int N, M;
int DP[2001][2001];
int arr[2001];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    memset(DP, 0, sizeof(DP));

    for (int i = 1; i <= N; ++i)
    {
        int _input;
        cin >> _input;
        arr[i] = _input;
        DP[i][i] = 1;
    }

    for (int i = 1; i <= N-1; ++i)
    {
		if (arr[i] == arr[i+1])
			DP[i][i+1] = 1;
	}

    for (int i = N-1; i >= 1; --i)
    {
		for (int j = i+2; j <= N; ++j)
        {
			if (arr[i] == arr[j] && DP[i+1][j-1] == 1) {
				DP[i][j] = 1;
			}
		}
	}

    cin >> M;

    for (int i = 0; i < M; ++i)
    {
        int start, end;
        cin >> start >> end;

        cout << DP[start][end] << '\n';
    }

    return 0;
}
```
<br/>

제한시간이 0.5초이고, N의 값이 2000이하 인 것을 보고 설마 했는데<br/>

알고리즘 유형을 열어보니 다이나믹 프로그래밍이었다.<br/>

그래서 우선 DP 배열을 만들어주었다.<br/>

```
int DP[2001][2001];
int arr[2001];

...

for (int i = 1; i <= N; ++i)
{
    int _input;
    cin >> _input;
    arr[i] = _input;
    DP[i][i] = 1;
}
```

이 DP 배열은 DP[S][E]가 있다고 할때,<br/>

S지점부터 E 지점까지가 팰린드롬인지를 저장하는 배열이다.<br/>

자, 문제는 이 DP배열을 어떻게 채우느냐였다.<br/>

이 부분은 [이 분의 글](https://4z7l.github.io/2021/04/07/algorithms-boj-10942.html)을 참고하였다.<br/>

```
DP[i][i] = 1;
```
우선, 혼자 있는 경우는 무조건 팰린드롬이므로 i부터 i까지 해당되는 부분은 1을 채워준다.<br/>

```
for (int i = 1; i <= N-1; ++i)
{
    if (arr[i] == arr[i+1])
        DP[i][i+1] = 1;
}
```

그리고 이웃한 칸이 같다면 팰린드롬을 채워나간다.<br/>

```
for (int i = N-1; i >= 1; --i)
{
    for (int j = i+2; j <= N; ++j)
    {
        if (arr[i] == arr[j] && DP[i+1][j-1] == 1) {
            DP[i][j] = 1;
        }
    }
}
```

그 다음에 S ~ E 까지가 팰린드롬이려면 S+1 ~ E-1 까지도 팰린드롬이어야 한다는 점을 이용한다.<br/>

이렇게해서 DP를 채워주면 된다.<br/>
