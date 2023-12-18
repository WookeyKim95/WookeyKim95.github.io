---
layout: post
title:  "[백준] 2023_신기한 소수 C++"
subtitle:   
date: 2023-12-18 06:22:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2023_신기한 소수 C++<br/>
<br/>

백트래킹을 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int N;
int prime[4] = {2, 3, 5, 7};

bool isPrime(int num)
{
    if (num < 2)
        return false;

    for(int i = 2; i * i <= num; ++i)
    {
        if(num % i == 0)
            return false;
    }
    return true;
}

void backtrack(int first, int n)
{
    if (n == 0)
        cout << first << '\n';

    for (int i = 1; i < 10; i += 2)
    {
        int temp = first * 10 + i;

        if (isPrime(temp))
            backtrack(temp, n-1);
    }       
}


int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    
    for (int i = 0; i < 4; ++i)
        backtrack(prime[i], N-1);

    return 0;
}
```

처음에는 그냥 완전탐색으로 일일이 경우를 따져야하나?<br/>

생각했는데 N = 8, 즉 10000000 ~ 99999999 까지 수를 다 따지려면 시간이 오지게 초과될 것이 뻔했다.<br/>

그래서 생각한 방법은

- 소수를 하나 쌓는다.
- 홀수를 더 쌓아준다.
- 만약에 그 수가 소수라면 다음 단계에서 같은 과정을 진행한다.

즉, 2, 3, 5, 7로 시작한 다음에 21, 23, 25, 27, ... 이렇게 경우를 모두 판단해주는 것이다.<br/>

여기서, 23, 29가 소수니까 231, 233, 235, ... 이렇게 다음 단계가 진행될 것이다.<br/>

참, 그리고 홀수만 더해줘야하는 이유는 짝수가 끝에 들어가면 무조건 소수가 아니기 때문에<br/>

가지치기를 해줌으로써 시간복잡도를 줄이는 것이다!<br/>