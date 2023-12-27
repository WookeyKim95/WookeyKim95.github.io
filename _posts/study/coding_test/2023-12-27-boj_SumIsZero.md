---
layout: post
title:  "[백준] 3151_합이 0 C++"
subtitle:   
date: 2023-12-27 07:15:13 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 3151_합이 0 C++<br/>
<br/>

완전탐색 및 투 포인터를 쓰는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int N;
vector<int> arr;

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N;

	for (int i = 0; i < N; ++i)
	{
		int _input;
		cin >> _input;
		arr.push_back(_input);
	}

	sort(arr.begin(), arr.end());

	int ans = 0;
	for (int i = 0; i < N; ++i)
	{
		int Cur = arr[i];
		if (Cur > 0)
			break;

		int left = i+1, right = N-1;

		while (left < right)
		{
			int Sum = arr[left] + arr[right];

			if (i == left)
			{
				left++;
				continue;
			}

			if (i == right)
			{
				right--;
				continue;
			}	

			if (Sum == -Cur)
			{
				ans++;
				left++;
			}

			else if (Sum < -Cur)
				left++;
			else
				right--;
		}
	}

	cout << ans;

	return 0;
}
```

<br/>

## 오답제출코드<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int N;
vector<int> arr;

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> N;

	for (int i = 0; i < N; ++i)
	{
		int _input;
		cin >> _input;
		arr.push_back(_input);
	}

	sort(arr.begin(), arr.end());

	int ans = 0;
	for (int i = 0; i < N; ++i)
	{
		int Cur = arr[i];

		int left = 0, right = N-1;

		while (left < right)
		{
			int Sum = arr[left] + arr[right];

			if (i == left)
			{
				left++;
				continue;
			}

			if (i == right)
			{
				right--;
				continue;
			}	

			if (Sum == -Cur)
			{
				ans++;
				left++;
			}

			else if (Sum < -Cur)
				left++;
			else
				right--;
		}
	}

	cout << ans;

	return 0;
}
```

여기서 발생하는 문제는 중복문제였다.<br/>

같은 경우를 또 세는 경우가 발생했기 때문에 오답이 발생하였다.<br/>

이것을 방지하기 위해서

```
arr[i] > 0
```
위와 같은 경우에는 break를 하도록 조치하였고,<br/>

[참고링크](https://skdltm117.tistory.com/71)를 참고하여 구체적인 코드를 구현하였다.<br/>

그리고 마지막으로 ans 범위가 int범위를 넘어가기 때문에 long long으로 받아야하는 것은 덤.<br/>