---
layout: post
title:  "[백준] 1450_냅색문제 C++"
subtitle:   
date: 2023-08-28 07:27:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1450_냅색문제 C++<br/>
<br/>

이분탐색, 정확히는 Upper_Bound의 활용에 대해서 알아보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

typedef long long ll;

int N, C, ans = 0;
vector<ll> product;
vector<ll> LeftSum;
vector<ll> RightSum;

void Left(ll idx, ll sum)
{
    if (idx == N/2)
    {
        LeftSum.push_back(sum);
        return;
    }
    Left(idx+1, sum);
    Left(idx+1, sum + product[idx]);
}

void Right(ll idx, ll sum)
{
    if (idx == N)
    {
        RightSum.push_back(sum);
        return;
    }
    Right(idx+1, sum);
    Right(idx+1, sum + product[idx]);
}


int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> C;

    for (int i = 0; i < N; ++i)
    {
        ll _input;
        cin >> _input;
        product.push_back(_input);
    }

    Left(0, 0);
    Right(N/2, 0);

    sort(RightSum.begin(), RightSum.end());

    for (size_t i = 0; i < LeftSum.size(); ++i)
        ans += upper_bound(RightSum.begin(), RightSum.end(), C - LeftSum[i]) - RightSum.begin();
    
    cout << ans;

    return 0;
}
```

우선 전체적인 코드는 [이분의 글](https://allmymight.tistory.com/99)을 참고하였다.<br/>

알고리즘 분류를 펼쳐보니 이분탐색이라고 하길래<br/>

```
while (start <= end)
{
    int mid = (start + end) / 2;

    ...
}
```
구조를 준비하였다.<br/>

근데 웬걸, 이분탐색을 어떻게 써야할지도 모르겠고<br/>

그래서 참고를 해보니 이분탐색은 없고 Upper_Bound라는 낮선 함수를 다들 사용한 것이 아닌가.<br/>

그래서 UpperBound에 대해서 검색을 해보았다.<br/>

그리고 전체적인 풀이 코드에 대해서 분석하자면<br/>

물건 하나는 그것을 넣을지 안넣을지를 결정할 수 있다.<br/>

근데 최대 30가지의 물건이기 때문에 이런 테스트케이스가 나온다면 2^30이기 때문에<br/>

이를 결정하는 것만으로도 시간초과가 날 수 있다.<br/>

그래서 반을 나누고, 왼쪽에서부터 경우의 수를 따진다.<br/>

```
void Left(ll idx, ll sum)
{
    if (idx == N/2)
    {
        LeftSum.push_back(sum);
        return;
    }
    Left(idx+1, sum);
    Left(idx+1, sum + product[idx]);
}
```

만약에 idx가 N/2, 즉 전체의 반에 도달했다면 나올 수 있는 합계, 즉 Sum 목록에다가 더한다.<br/>

```
LeftSum(idx+1, sum) // 물건을 안넣고 넘어간경우
LeftSum(idx+1, sum + product[idx]) // 물건을 넣고 넘어간경우
```

이렇게 해석을 하면 된다.<br/>

이제 오른쪽 벡터를 정렬하고 왼쪽 벡터를 순회하며 왼쪽 벡터의 원소가 K 일 때<br/>

오른쪽 원소가 C-K 보다 작은 경우를 추가하면 된다.<br/>

여기서 순회를 할 때 linear순회보다는 이분탐색을 통한 순회를 해야한다.<br/>

즉, upper_bound는 이분탐색을 이용하는 함수이며, 원하는 원소보다 큰 원소의 위치를 찾게해주는 것이다.<br/>

이 문제가 알고리즘 분류 중 이분탐색이 있는 이유이기도 하다.<br/>

```
template <class ForwardIt, class T>
ForwardIt upper_bound(ForwardIt first, ForwardIt last, const T& value) {
  ForwardIt it;
  typename std::iterator_traits<ForwardIt>::difference_type count, step;
  count = std::distance(first, last);

  while (count > 0) {
    it = first;
    step = count / 2;
    std::advance(it, step);
    if (!(value < *it)) {
      first = ++it;
      count -= step + 1;
    } else
      count = step;
  }
  return first;
}
```
그리고 반대로 lower_bound함수도 있다.<br/>

```
template <class ForwardIt, class T>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value) {
  ForwardIt it;
  typename std::iterator_traits<ForwardIt>::difference_type count, step;
  count = std::distance(first, last);

  while (count > 0) {
    it = first;
    step = count / 2;
    std::advance(it, step);
    if (*it < value) {
      first = ++it;
      count -= step + 1;
    } else
      count = step;
  }
  return first;
}
```


두 코드의 출처는 [여기](https://modoocode.com/298)이다.<br/>

