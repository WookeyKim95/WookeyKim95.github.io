---
layout: post
title:  "[백준] 2960번_에라토스테네스의 체 C++"
subtitle:   
date: 2023-02-20 11:56:32 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2960번_에라토스테네스의 체 C++<br/>
<br/>

에라토스테네스의 체를 구현하는 문제<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

bool isPrime(int a)
{
    for (int i = 2; i*i <= a; ++i)
    {
        if (a % i == 0)
            return false;
    }
    return true;
}

int main()
{
    int N, K;
    cin >> N >> K;

    vector<int> v;
    int count = 2;

    while(count <= N)
    {
        if (isPrime(count))
        {
            for (int i = 1; i <= N / count; ++i)
            {
                if (find(v.begin(), v.end(), count * i) == v.end())
                    v.push_back(count * i);
            }
        }
        count++;
    }

    cout << v[K-1];

    return 0;
}
```

정수를 담을 벡터를 준비한다.<br/>

그리고 2부터 시작해서 count가 N보다 작거나 같을 동안 아래 과정을 반복한다.<br/>

- N보다 작거나 같은 count의 배수를 벡터에 넣어준다.
- 이때, 이미 벡터에 있는 수일경우 생략한다.
- count의 배수를 모두 넣었을 경우 다음 수를 count로 지정하고 소수인지 검사한다.
- 소수라면 위의 과정을 반복한다.

그래서 벡터로 넣고 나서 그다음에 K번째수가 무엇인지를 추출하는 방법을 썼다.<br/>

이 글을 적으면서 생각해보니, 수를 벡터에 넣을 때마다 카운터를 증가시켜서 카운터가 K가 되었을 때<br/>

그 수를 리턴하고 코드를 종료하는 방법도 생각이 났다.<br/>

수를 약 1000개 넣어놓고 1번째 의 수가 무엇인지 검사를 하려면 그 방법이 더 빠르겠지.<br/>