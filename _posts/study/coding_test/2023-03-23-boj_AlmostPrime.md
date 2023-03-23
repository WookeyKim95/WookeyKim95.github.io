---
layout: post
title:  "[백준] 1456번_거의 소수 C++"
subtitle:   
date: 2023-03-23 09:25:41 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1456번_거의 소수 C++<br/>
<br/>

에라토스테네스의 체를 응용하는 수학 문제.<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <cmath>
#include <cstring>
#include <vector>

using namespace std;

bool isPrime[10000001] = {true};

long long A, B;
vector<long long> Primes;

void CheckNonePrime()
{
    memset(isPrime, true, sizeof(isPrime));

    for (int i = 2; i * i <= 10000000; ++i)
    {
        if (isPrime[i] == false)
            continue;
        
        for (int w = i, j = i; w * j <= 10000000; j++)
        {
            isPrime[w * j] = false;
        }
    }

    for (long long i = 2; i * i <= B; ++i)
    {
        if (isPrime[i])
            Primes.push_back(i);
    }
    return;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);

    cin >> A >> B;

    CheckNonePrime();

    int count = 0;

    for (size_t i = 0; i < Primes.size(); ++i)
    {
        long long num = Primes[i];

        while (Primes[i] <= B / num)
        {
            if (Primes[i] * num >= A)
                count++;
            num *= Primes[i];
        }
    }

    cout << count;
    return 0;
}
```
<br/>

소수인지 여부를 판별하는 리스트와 소수를 담은 벡터를 활용해야 속도가 빨라진다.<br/>

시간초과가 걸렸었는데 그 과정을 아래에서 서술한다.<br/>

<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <cmath>

using namespace std;

bool isPrime(long long a)
{
    if (a == 1)
        return false;
    
    for (long long i = 2; i * i <= a; ++i)
    {
        if (a % i == 0)
            return false;
    }
    return true;
}

bool isGPrime(long long a)
{
    if (a == 1)
        return false;
    
    for (long long i = 2; i*i <= a; ++i)
    {
        if (isPrime(i))
        {
            long long counter = i;
            for (long long j = 2; counter <= a; ++j)
            {
                counter *= i;
                if (counter == a)
                    return true;
            }
        }
    }
    return false;
}

int main()
{
    long long A, B;
    cin >> A >> B;

    int count = 0;
    for (long long i = A; i <= B; ++i)
    {
        if (isGPrime(i))
            count++;
    }

    cout << count;
    return 0;
}
```

처음엔 pow함수를 활용했는데

pow함수가 double형을 반환해서 그런지 조건을 맞추는데 애를 먹었다.<br/>

그래서 pow함수를 쓰는 대신에 for문을 아래와 같이 작성하였다.<br/>

```
    for (long long i = 2; i*i <= a; ++i)
    {
        if (isPrime(i))
        {
            long long counter = i;
            for (long long j = 2; counter <= a; ++j)
            {
                counter *= i;
                if (counter == a)
                    return true;
            }
        }
    }
```

counter를 배치한 다음에 a보다 낮은 동안에 counter를 계속해서 같은 수를 곱하는 것이다.<br/>

하지만 우선 수는 다 맞추었지만 시간이 오래걸린다는 점이 있었다.<br/>

실제로 아래 테스트 케이스를 실행 시켰더니 기댓값이 나왔지만 시간이 12초 걸렸다.<br/>

```
5324 894739

// 기댓값 : 183
// 출력값 : 183 (소요시간 12초)
```

그럼 로직은 잘 맞추었다는 것인데 어떻게 시간을 단축시킬 수 있을까?
<br/>

검색을 통해 [참고링크](https://codevang.tistory.com/65)를 찾았다.<br/>

에라토스테네스의 체는 아래와 같이 활용하는 것이 좋다고 한다.<br/>

```
bool isPrime[10000001] = {true};

void CheckNonePrime()
{
    memset(isPrime, true, sizeof(isPrime));

    for (int i = 2; i * i <= 10000000; ++i)
    {
        if (isPrime[i] == false)
            continue;
        
        for (int w = i, j = i; w * j <= 10000000; j++)
        {
            isPrime[w * j] = false;
        }
    }
}
```
각 인덱스 값에 우선 true를 부여한 다음에<br/>

소수를 제외한 배수를 곱해주면서 만나는 값을 false로 바꾸는 방식이다.<br/>

이 코드를 넣었더니<br/>

12초 걸리던 테스트 케이스가 2초로 줄어들었다.<br/>

하지만 계속해서 시간초과가 떴다.<br/>
<br/>

## 정답코드로의 전환<br/>
<br/>

[참고링크](https://jaimemin.tistory.com/1117)를 참고하여 방식을 바꾸었다.<br/>

범위 숫자 하나하나 마다 확인을 해주는 것이 아니라,<br/>

소수의 몇제곱 꼴, 즉 거의 소수가 범위 안에 몇개를 있는지 확인해주는 방식으로.<br/>

따라서 소수를 계속 제곱시켜서 A와 B안에 몇개가 있는지 확인하는 식으로 구현하였다.<br/>

그래서 나온 정답코드가 위의 정답코드이다.<br/>
