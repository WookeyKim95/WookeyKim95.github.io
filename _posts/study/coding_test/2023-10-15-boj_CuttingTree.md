---
layout: post
title:  "[백준] 2805_나무 자르기 C++"
subtitle:   
date: 2023-10-15 07:19:37 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2805_나무 자르기 C++<br/>
<br/>

기초적인 매개변수를 통한 이분탐색 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

vector<int> Trees;
int N, M;

bool isPossible(int height)
{
	int target = 0;
	for (int i = 0; i < N; ++i)
    {
		if (Trees[i] >= height)
			target += (Trees[i] - height);

		if (target >= M)
            return true;
	}
	return false;
}

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
        Trees.push_back(_input);
    }

    int left = 0;
    int right = 1000000000;
    int ans;
    while(left <= right)
    {
        int mid = (left + right) / 2;
        
        if (isPossible(mid))
        {
            left = mid+1;
            ans = mid;
        }
        else
            right = mid-1;
    }

    cout << ans;
    
    return 0;
}
```

매개변수를 통한 이분탐색의 탬플릿은 아래와 같다.

```
int left = 최솟값;
int right = 최댓값;
int ans;
while(left <= right)
{
    int mid = (left + right) / 2;
    
    if (mid 값이 가능한가?)
    {
        left = mid+1;
        ans = mid;
    }
    else
        right = mid-1;
}

cout << ans;
```

그렇다면 문제는 여기서는 mid 값이 가능한지 조건을 어떻게 설계하느냐이다.<br/>

간단하다. mid만큼 나무를 자르면 가져가고 싶은 길이를 충족할 수 있는가를 설정한다.<br/>

그 코드가 아래의 코드이다.<br/>

```
bool isPossible(int height)
{
	int target = 0;
	for (int i = 0; i < N; ++i)
    {
		if (Trees[i] >= height)
			target += (Trees[i] - height);

		if (target >= M)
            return true;
	}
	return false;
}
```

조건을 만족하면 left를, 그렇지 않다면 right를 옮기도록 구현하였다.<br>