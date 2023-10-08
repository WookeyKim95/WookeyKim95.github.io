---
layout: post
title:  "[백준] 11728_배열 합치기 C++"
subtitle:   
date: 2023-10-08 06:35:49 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 11728_배열 합치기 C++<br/>
<br/>

알고보면 투 포인터를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    vector<int> A;
    vector<int> B;
    int N, M;
    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        A.push_back(_input);
    }

    for (int i = 0; i < M; ++i)
    {
        int _input;
        cin >> _input;
        B.push_back(_input);
    }

    int AP = 0, BP = 0;

    while (AP < N && BP < M)
    {
        if (A[AP] > B[BP])
        {
            cout << B[BP] << ' ';
            BP++;
        }
        else
        {
            cout << A[AP] << ' ';
            AP++;
        }
    }

    while (AP < N)
    {
        cout << A[AP] << ' ';
        AP++;
    }

    while (BP < M)
    {
        cout << B[BP] << ' ';
        BP++;
    }

    return 0;
}
```

배열을 정렬하고 합치라고 하니까 진짜로 합친 뒤 정렬을 해야할 지도 모른다.<br/>

하지만 주목해보면 이번 문제의 테스트케이스에서 N,M의 최대값은 100만이다.<br/>

길이가 100만인 두 배열을 합치는 과정도 오래걸릴 뿐더러,<br/>

정렬하는 시간까지 더하면 시간초과가 날 것이 뻔했다.<br/>

그러면 어떻게 하면 좋을까?<br/>

주목해보자! 이 문제에서 핵심은 아래와 같다.

- **정렬되어 있는** 두 배열
- 배열을 합친 뒤 **정렬**하시오.

맞다. 두 배열은 정렬되어있는 상태이다.<br/>

그렇다면! 맨 처음부터 크기를 비교해서 더 작은 값을 먼저 배열에 넣어주면<br/>

정렬을 다시 할 필요도 없이 정렬이 완료된 배열이 완성되는 것이 아닐까?<br/>

이렇게 사용된 것이 투 포인터이다.<br/>

```
1 4 5 7 9
*

2 3 6 8
*
```
현재 아래에 별이 있는 지점에서 비교를 해보면

2보다 1이 더 작으므로, 1을 넣어준 뒤 *을 한 칸 오른쪽으로 옮긴다.<br/>

```
1 4 5 7 9
  *

2 3 6 8
*

// 완성할 배열
1
```

그 다음에 다시 별이 가리키는 지점을 비교해보면<br/>

4보다 2가 더 작으므로 2를 넣어준 뒤 *을 한 칸 오른쪽으로 옮긴다.<br/>

```
1 4 5 7 9
  *

2 3 6 8
  *

// 완성할 배열
1 2
```

그리고 다시 비교를 해보면 4보다 3이 더 작으므로 3을 넣어준다.<br/>

```
1 4 5 7 9
  *

2 3 6 8
    *

// 완성할 배열
1 2 3
```

다시 비교를 해보면 이번엔 4를 넣어준다.<br/>

```
1 4 5 7 9
    *

2 3 6 8
    *

// 완성할 배열
1 2 3 4
```

이 과정을 별이 한쪽 배열 끝을 넘어 도달할 때 까지 반복해준 다음에<br/>

나머지 배열의 남은 값들을 넣어주면 배열이 바로 완성된다.<br/>

그 과정을 구현한 부분이 바로 이 코드이다.<br/>

```
int AP = 0, BP = 0;

while (AP < N && BP < M)
{
	if (A[AP] > B[BP])
	{
		cout << B[BP] << ' ';
		BP++;
	}
	else
	{
		cout << A[AP] << ' ';
		AP++;
	}
}

while (AP < N)
{
	cout << A[AP] << ' ';
	AP++;
}

while (BP < M)
{
	cout << B[BP] << ' ';
	BP++;
}
```

이렇게 투 포인터의 원리를 사용하여 풀 수 있는 문제였다.<br/>