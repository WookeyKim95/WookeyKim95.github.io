---
layout: post
title:  "[백준] 1208_부분수열의 합 2 C++"
subtitle:   
date: 2023-07-08 08:21:18 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1208_부분수열의 합 2 C++<br/>
<br/>

알고보면 반으로 나눠보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <map>
#include <algorithm>

using namespace std;

int N, S;
int arr[41];
map<int, int> total;
long long cnt = 0;

void leftSum(int s, int sum)
{
    if (s == N/2)
    {
        total[sum]++;
        return;
    }
    leftSum(s+1, sum);
    leftSum(s+1, sum+arr[s]);
}

void rightSum(int s, int sum)
{
    if (s == N)
    {
        cnt += total[S-sum];
        return;
    }
    rightSum(s+1, sum);
    rightSum(s+1, sum+arr[s]);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> S;

    for (int i = 0; i < N; ++i)
        cin >> arr[i];

    sort(arr, arr + N);

    leftSum(0, 0);
    rightSum(N/2, 0);

    if (S == 0)
        cout << cnt - 1;
    else
        cout << cnt;

    return 0;
}
```

[참고링크](https://allmymight.tistory.com/98)를 참고해서 풀었다.<br/>

<br/>

투 포인터인지, 이분 탐색인지 생각을 했지만,<br/>

어떻게 풀어야할지 감이 오지 않아서 검색을 해보았다.<br/>

이 문제의 취지는 시간복잡도가 O(2^40)이 나오는 것을 O(2^20 * 2)로 푸는 것이었다.<br/>

우선, 수들의 합과 그 경우의 수를 담을 map을 구성을 해준다.<br/>

```
map<int, int> total; // <수들의 합, 나오는 경우의 수>
```

왼쪽 부분은 개수를 저장을 하고,<br/>

오른쪽 부분은 구한 부분 수열의 합이 sum 이면 map[S-sum]의 값<br/>

왼쪽 부분에서 계산을 할 때, 합이 S-sum이었던 부분과 더하면 S가 만들어지기 때문에<br/>

결과값 cnt에 map[S-sum]을 더해준다.<br/>

이 때, S = 0이면 둘 다 공집합인 경우를 제외시킨다.<br/>

