---
layout: post
title:  "[백준] 10826_피보나치 수 4 C++"
subtitle:   
date: 2024-01-29 06:45:39 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 10826_피보나치 수 4 C++<br/>
<br/>

큰 수 연산에 대해서 신경써야 하는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <string>
#include <algorithm>

#define MAX 10001

using namespace std;

int N;

string DP[MAX];

string Sum(string a, string b)
{
    int Num = 0;
    int Carry = 0;
    string ret = "";

    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());

    while (a.length() < b.length())
        a += "0";
    
    while (a.length() > b.length())
        b += "0";
    
    for (size_t i = 0; i < a.length(); ++i)
    {
        Num = ((a[i] - '0') + (b[i] - '0') + Carry) % 10;
        ret += to_string(Num);
        Carry = ((a[i] - '0') + (b[i] - '0') + Carry) / 10;
    }

    if (Carry != 0)
        ret += to_string(Carry);
    
    reverse(ret.begin(), ret.end());

    return ret;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    DP[0] = "0";
    DP[1] = "1";

    for (int i = 2; i <= N; ++i)
        DP[i] = Sum(DP[i-1], DP[i-2]);
    
    cout << DP[N];

    return 0;
}
```

<br/>

사실 이 문제는 파이썬으로 풀면 훨씬 쉽다.<br/>

```
arr = [0] * 10001

arr[0] = 0
arr[1] = 1

for i in range(2, 10001):
    arr[i] = arr[i-1] + arr[i-2]

n = int(input())
print(arr[n])
```

그냥 이게 정답코드이다.<br/>

다만 C++에서는 어려운 이유가 큰 수 연산을 해야하기 때문이다.<br/>

파이썬에서는 기본적으로 탑재된 큰 수 연산 시스템이<br/>

C++에서는 탑재되어 있지 않다.<br/>

그래서 큰 수 연산 방법을 우리가 직접 구현해야 한다.<br/>

그리고 long long을 쓰는 것도 아니다. string을 써서 연산한 다음에 string으로 출력시켜야한다.<br/>

그 과정을 살펴보자.<br/>

[참고링크](https://blogshine.tistory.com/268)

```
string Sum(string a, string b)
{
    int Num = 0;
    int Carry = 0;
    string ret = "";

    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());

    while (a.length() < b.length())
        a += "0";
    
    while (a.length() > b.length())
        b += "0";
    
    for (size_t i = 0; i < a.length(); ++i)
    {
        Num = ((a[i] - '0') + (b[i] - '0') + carry) % 10;
        ret += to_string(num);
        carry = ((a[i] - '0') + (b[i] - '0') + carry) / 10;
    }

    if (carry != 0)
        ret += to_string(carry);
    
    reverse(ret.begin(), ret.end());

    return ret;
}
```

- 받은 string 2개를 reverse로 뒤집는다. 그래야 flor문을 돌 때 1의 자리부터 더할 수 있다.
- 그 다음으로는 두 string의 길이를 맞춰준다.
- 그리고 string을 더한다.

이런 과정으로 진행되고 코드는 위와 같다.