---
layout: post
title:  "[백준] 1946_신입 사원 C++"
subtitle:   
date: 2023-12-25 07:26:29 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1946_신입 사원 C++<br/>
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


int T, N;

int main()
{
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);

	cin >> T;

	for (int i = 0; i < T; i++)
	{
		cin >> N;
		vector<pair<int,int>> score;
		for (int j = 0; j < N; j++)
		{
			int A, B;
			cin >> A >> B;
			score.push_back({A, B});
		}

		sort(score.begin(), score.end());


		int max_score = score[0].second;
		int cnt = 0;
		for (int i = 0; i < N; i++)
		{
			if (score[i].second <= max_score)
			{
				cnt++;
				max_score = score[i].second;
			}
		}

		cout << cnt << '\n';
		
	}

	return 0;
} 
```

정렬을 해서 푸는 그리디 알고리즘 문제였다.<br/>

서류 점수로 정렬을 한 다음에 면접 점수를 비교하면서 카운트를 세주면 되는 문제였다.<br/>

그래서 pair로 점수를 받고 first로 정렬을 한 뒤 second에서 max를 저장해가며<br/>

비교하도록 구현하였다.<br/>