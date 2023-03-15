---
layout: post
title:  "[백준] 1351번_무한 수열 C++"
subtitle:   
date: 2023-03-16 08:43:42 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1351번_무한 수열 C++<br/>
<br/>

다이나믹 프로그래밍, 재귀함수, 그리고 map을 이용해서 풀었던 문제<br/>

아직 DP에 익숙하지 않지만 재귀 함수와 for문을 적극적으로 사용해서 실력을 늘려야겠다.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <map>

using namespace std;

long long N, P, Q;
map<long long, long long> dict;

long long recur(long long n)
{
    auto iter = dict.find(n);
    if (n == 0)
        return 1;
    else if (iter != dict.end())
        return iter->second;

    dict.insert({n, recur(n / P) + recur(n / Q)});
    return dict.find(n)->second;
}

int main()
{
    cin >> N >> P >> Q;

    dict.insert({0, 1});

    cout << recur(N);

    return 0;
}
```

map을 사용하지 않고 재귀함수만을 사용하면 시간초과가 발생한다.<br/>

정답을 도출하기까지 겪었던 과정을 아래에 서술한다.<br/>

<br/>

## 오답제출코드(에러) <br/>
<Br/>

```
#include <iostream>

using namespace std;

#define INF 1000000000000

long long arr[INF];

int main()
{
    long long N, P, Q;
    cin >> N >> P >> Q;

    arr[0] = 0;

    for (int i = 1; i <= N; ++i)
    {
        arr[i] = arr[i/P] + arr[i/Q];
    }
    
    cout << arr[N];

    return 0;
}
```

문제가 이렇게 쉽게 풀릴까? 생각하면서 답을 제출해보았다.<br/>

그러자 터미널에 뜬 내용은 아래와 같았다.<br/>

```
error: size of array 'arr' is too large
 long long arr[INF];
```

아 이게 이 문제의 핵심이구나 생각이 들었다.<br/>

큰 수의 범위를 정해서 테이블을 미리 선언하지 못하도록 한 것이다.<br/>

그래서 문제 난이도가 골드5급으로 평가된 것 같다.<br/>

이 때 문제의 사용 알고리즘을 살펴보니

- 다이나믹 프로그래밍
- 자료구조
- 해시를 사용한 집합과 맵

이었다.<br/>

음! 맵을 사용하라는 힌트를 얻어서 맵을 사용하도록 수정하였다.<br/>
<br/>

## 오답제출코드 (시간초과)<br/>
<br/>

```
#include <iostream>
#include <map>

using namespace std;

int main()
{
    long long N, P, Q;

    cin >> N >> P >> Q;

    map<long long, long long> nums;

    nums.insert({0, 1});

    for (long long i = 1; i <= N; ++i)
    {
        auto iter1 = nums.find(i/P);
        auto iter2 = nums.find(i/Q);

        nums.insert({i, iter1->second + iter2->second});
    }

    cout << nums.find(N)->second;

    return 0;
}
```

그래서 완성한 코드가 이거다.<br/>

음.. for문을 사용하다보니 N이 많이 크면 시간이 오래걸릴 것 같았다.<br/>

한번 테스트를 해보니

```
10000000 3 3
```
아니나 다를까 위 테스트케이스, 즉 예제입력 3번에서 작동시간이 10초가 넘었다.<br/>

for문을 사용하는 것이 아니라는 건데.. 아니면 연산 과정을 최소화 하라는 건데..<br/>

DP에 익숙하지 않았던 나는 구글링을 통한 [참고링크](https://velog.io/@deannn/BOJ-%EB%B0%B1%EC%A4%80-1351%EB%B2%88-%EB%AC%B4%ED%95%9C-%EC%88%98%EC%97%B4-Python)를 힌트를 얻어내었다.<br/>

오호라! 재귀함수를 사용하는 방법이 있었네!<br/>

피보나치 수열도 재귀함수와 for문을 사용해서 풀면 되는 것인데,<br/>

이 문제는 재귀함수를 사용해서 풀어야 했나보다.<br/>

그래서 작성한 코드가 아래코드와 같다.<br/>
<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>

using namespace std;

long long N, P, Q;

long long recur(long long n)
{
    if (n == 0)
        return 1;
    
    return recur(n/P) + recur(n/Q);
}

int main()
{
    cin >> N >> P >> Q;

    cout << recur(N);

    return 0;
}
```
<br/>

우선 N의 입력값이 21억을 넘어가므로 long long 자료형을 사용하였다.<br/>

재귀 함수와 for문을 사용해서 DP를 풀 수 있다.<br/>

이 중에서 나는 재귀함수를 사용하였다.<br/>

recur함수를 통해서 계속해서 나눈 다음 n == 0일때 1을 반환해서 그 더한 값을 반환하도록 구현하였다.<br/> 

하지만 시간초과가 떴다 흠..<br/>

map을 쓰지 않아서 그런 것 같다.<br/>

그래서 위에 언급한 [참고링크](https://velog.io/@deannn/BOJ-%EB%B0%B1%EC%A4%80-1351%EB%B2%88-%EB%AC%B4%ED%95%9C-%EC%88%98%EC%97%B4-Python)를 다시 참고하여 map을 써서 구현하였다.<br/>

그래서 작성한 것이 위에 작성한 정답제출코드이다.<br/>