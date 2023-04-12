---
layout: post
title:  "[백준] 1111번_IQ Test C++"
subtitle:   
date: 2023-04-12 09:07:10 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1111번_IQ Test C++<br/>
<br/>

완전탐색 알고리즘을 활용하는 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;
int arr[50];
vector<pair<int, int>> v;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
        cin >> arr[i];
    
    if (N == 1)
    {
        cout << 'A';
        return 0;
    }

    else if (N == 2)
    {
        if (arr[0] == arr[1])
			cout << arr[1];
		else
			cout << 'A';
		return 0;
    }

    else
    {
        int a = 0;
		if (arr[1] - arr[0] != 0)
			a = (arr[2] - arr[1]) / (arr[1] - arr[0]);

		int b = arr[1] - arr[0] * a;
		
		for (int i = 1; i < N; i++){
			if (arr[i] != arr[i - 1] * a + b){
				cout << 'B';
				return 0;
			}
		}
		
		cout << arr[N - 1] * a + b;
    }

    return 0;
}

```

오답 제출 코드에 비해서 조금 더 간소화 되었다.<br/>

우선, N = 1이면 반드시 여러가지이기 때문에 A를 출력한다.<br/>

그리고 N = 2이면 같은 값이면 같은 값을 출력하면 되고,<br/>

다른 값이면 규칙을 알 수 없기 때문에 B를 출력한다.<br/>

그리고 N이 3 이상이라면 규칙을 찾는다.<br/>

규칙을 적용해서 값이 모두 규칙을 따라야 다음 수를 출력할 수 있기 때문에<br/>

만약에 규칙을 적용한 값이 다르다면 규칙을 알 수 없기 때문에 B를 출력한다.<br/>

만약에 규칙을 모두 따른다면 다음 값을 출력한다.<br/>

<br/>

## 오답제출코드<br>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;
int arr[50];
vector<pair<int, int>> v;

int main()
{
    cin >> N;

    for (int i = 0; i < N; ++i)
        cin >> arr[i];
    
    if (N == 1)
    {
        cout << 'A';
        return 0;
    }

    for (int a = -100; a <= 100; ++a)
    {
        for (int b = -200; b <= 200; ++b)
        {
            for (int i = 0; i < N-1; ++i)
            {
                if (arr[i+1] != a*arr[i] + b)
                    break;

                if (i == N-2)
                    v.push_back(make_pair(a, b));
                
            }
        }
    }

    if (v.size() == 0)
        cout << 'B';
    else if (v.size() > 1)
    {
        for (size_t i = 1; i < v.size(); ++i)
        {
            int prev = arr[N-1]*v[i-1].first + v[i-1].second;
            int cur = arr[N-1]*v[i].first + v[i].second;

            if (prev != cur)
            {
                cout << 'A';
                break;
            }
            else if (i == v.size()-1)
                cout << arr[N-1]*v[0].first + v[0].second;
        }
    }
        
    else
        cout << arr[N-1]*v[0].first + v[0].second;

    return 0;
}
```

보통 같으면 완전탐색 알고리즘을 쓸 경우 시간초과가 뜰 것이다.<br/>

하지만 이 경우는 탐색하는 경우의 수가 적으므로 for문 여러개를 이용해서<br/>

완전탐색을 시도해볼 수 있다.<br/>

우선, N = 1인 경우 무조건 경우의 수는 여러가지이므로<br/>

바로 A를 출력하고 끝내는 것으로 하였다.<br/>

그리고 다음으로 문제에서 주어지는 정수들의 절댓값은 100 이하라고 했기 때문에<br/>

a의 범위는 -100~100, b의 범위는 -200~200일 것이다.<br/>

따라서 각 구간을 탐색하도록 구현하였다.<br/>

그리고 입력 값 사이의 규칙이 모두 동일하게 적용되는 a, b를 찾을 경우<br/>

벡터에 값을 넣어주도록 하였다.<br/>

그래서 벡터의 길이에 따라서 마지막에 분기를 나누게 된다.<br/>

이때, 마지막에 주의할 점은 벡터의 길이가 2이상일 때이다.<br/>

벡터의 길이가 2 이상이면 보통 답으로 'A'리고 생각하겠지만,<br/>

아래의 경우를 보자.

```
2
57 57
```

이 경우 벡터에 들어가는 (a, b)의 순서쌍을 보면 (0, 57), (1, 0)이 있다.<br/>

그러나 이 두 경우 모두 다음 값은 57이 나올 수 있다.<br/>

따라서 이 경우에는 'A'가 아니라 57을 출력해주어야한다.<br/>

그래서 벡터에 있는 (a, b)순서쌍을 모두 대입했는데 같은 값이 나온다면<br/>

그 값을 출력하도록 구현하였다.<br/>

하지만 결국엔 오답이었다.<br/>

범위를 200넘어서 까지 탐색해야 할 것 같은데<br/>

그러면 너무 방대해지기 때문에 이 방법은 포기.<br/>