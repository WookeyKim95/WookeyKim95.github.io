---
layout: post
title:  "[백준] 1354번_무한 수열2 C++"
subtitle:   
date: 2023-03-19 10:47:48 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1354번_무한 수열2 C++<br/>
<br/>

다이나믹 프로그래밍, 재귀함수, 그리고 map을 이용해서 풀었던 문제<br/>

1351번 무한수열과 연결되는 문제이다.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <map>

using namespace std;

long long N, P, Q, X, Y;
map<long long, long long> dict;

long long recur(long long n)
{
    auto iter = dict.find(n);
    if (n <= 0)
        return 1;

    else if (iter != dict.end())
        return iter->second;

    dict.insert({n, recur(n / P - X) + recur(n / Q - Y)});
    return dict.find(n)->second;
}

int main()
{
    cin >> N >> P >> Q >> X >> Y;

    dict.insert({0, 1});

    cout << recur(N);

    return 0;
}
```

1351번에 비해서 인덱스 계산 방법도 복잡해지고, 수의 범위가 더 커졌다.<br/>

그래서 채점하는데도 오래걸렸다.<br/>

풀이법은 1351번과 똑같이 dict역할을 해줄 map을 설정해주고,<br/>

해당 수가 이미 입력되었으면 그 값의 value 값을,<br/>

없으면 map에다가 key값으로, 그리고 그에 대응하는 값을 저장해주는 방식을 사용하였다.<br/>

그리고 0 이하의 수는 1을 가진다고 하였으므로<br/>

key값이 0이하라면 1을 return하도록 구현하였다.<br/>
<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <map>

using namespace std;

long long N, P, Q, X, Y;
map<long long, long long> dict;

long long recur(long long n)
{
    auto iter = dict.find(n);
    if (n <= 0)
    {
        if (iter == dict.end())
            dict.insert({n, 1});
        return 1;
    }
    else if (iter != dict.end())
        return iter->second;

    dict.insert({n, recur(n / P - X) + recur(n / Q - Y)});
    return dict.find(n)->second;
}

int main()
{
    cin >> N >> P >> Q >> X >> Y;

    dict.insert({0, 1});

    cout << recur(N);

    return 0;
}
```

<br/>


key값이 0 이하라면 1을 return하기만 하면 됐는데<br/>

꼼꼼히 체크를 한다고 key값이 있는지를 찾고 값을 반환하는 과정에서<br/>

탐색하는데 시간을 써버려서 시간초과가 났다.<br/>

그래서 탐색하는 과정의 코드를 빼주니 아슬아슬하게 통과가 되었다.<br/>