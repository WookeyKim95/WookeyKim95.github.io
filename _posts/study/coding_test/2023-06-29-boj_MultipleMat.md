---
layout: post
title:  "[백준] 11049_행렬 곱셈 순서 C++"
subtitle:   
date: 2023-06-29 07:57:19 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11049_행렬 곱셈 순서 C++<br/>
<br/>

DP를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>

#define INF 2147483647

using namespace std;

typedef pair<int, int> pii;

int N;
vector<pii> Mat;
int Sum[501], DP[501][501];

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    Mat.push_back({0, 0});

    for (int i = 1; i <= N; ++i)
    {
        int row, col;
        cin >> row >> col;

        Mat.push_back({row, col});
    }

   	for (int i = 1; i < N; ++i)
	{
		for (int j = 1; i+j <= N; ++j)
		{
			DP[j][i+j] = INF;
			for (int k = j; k <= i+j; ++k)
			{
				DP[j][i+j] = m_min(DP[j][i+j], DP[j][k] + DP[k+1][i+j] + Mat[j].first * Mat[k].second * Mat[i+j].second);
			}
		}

	}

	cout << DP[1][N];

    return 0;
}
```

시도를 해도 아이디어가 떠오르지 않아 결국 [이분의 글](https://cocoon1787.tistory.com/318)을 참고했다.<br/>

범위가 작기 때문에 3중 for문을 사용하신 것 같다.<br/>

일단 행렬 ABC를 곱하는데 (5x3), (3x2), (2x6)을 순서대로 곱하면 최종 모습은 (5x6) 형태를 띈다.<br/>

그리고 연산의 횟수는 순서에 따라서

- 5×3×2 + 5×2×6 (AB)C
- 3×2×6 + 5×3×6 A(BC)

가 된다. 이 중 작은 값을 고르면 된다.<br/>

이를 구현한 부분이 아래의 코드이다.<br/>

```
DP[j][i+j] = m_min(DP[j][i+j], DP[j][k] + DP[k+1][i+j] + Mat[j].first * Mat[k].second * Mat[i+j].second);
```

그리고 참고링크의 글에 따르면

- 가장 바깥쪽의 i는 구간 범위의 크기를 의미
- 가운데 for문에서 j는 구간 범위의 시작지점을 의미
- 가장 안쪽에 있는 for문에서 k는 구간 범위를 두 부분으로 나눌 때의 기준점

이다.<br/>
<Br/>


## 오답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

typedef pair<int, int> pii;

int N;
vector<pii> Mat;
int DP[501];

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int MultiMat(pii a, pii b, pii c)
{
    int ret = a.first * a.second * b.second;
    pii mid = {a.first, b.second};

    ret += mid.first * mid.second * c.second;

    return ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int row, col;
        cin >> row >> col;

        Mat.push_back({row, col});
    }

    DP[0] = 0;
    DP[1] = 0;
    DP[2] = 0;

    pii mid = Mat[0];
    for (int i = 0; i < N-2; ++i)
    {
        DP[i+3] = DP[i+2] + m_min(MultiMat(mid, Mat[i+1], Mat[i+2]), MultiMat(Mat[i+1], Mat[i+2], mid));
        mid = {mid.first, Mat[i+2].second};
    }

    cout << DP[N];

    return 0;
}
```
<br/>

처음엔 ABCDE가 있다고 하면 ABC부터 검사를 시작해서 (AB)C가 값이 더 작은지, A(BC)가 더 작은지 검사를 해준다.<br/>

이렇게 한 다음에 행렬의 행과 열을 새로 저장해준다.<br/>

그리고 ABC를 곱한 행렬을 F라고하면 FDE를 해줄 차례. F(DE), (FD)E 중에서 더 작은 값을 골라서 곱해준다.<br/>

이런 의도였는데 의도대로 되지 않았고, 잘못 생각한 것 같다.<br/>
