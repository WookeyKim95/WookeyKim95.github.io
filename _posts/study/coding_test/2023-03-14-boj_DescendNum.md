---
layout: post
title:  "[백준] 1038번_감소하는 수 C++"
subtitle:   
date: 2023-03-14 11:41:31 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1038번_감소하는 수 C++<br/>
<br/>

완전 탐색과 큐를 활용하는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int main()
{
    int N = 0;
    cin >> N;

    // 한자리 수는 그냥 출력하고 끝.
    if (N <= 9)
    {
        cout << N;
        return 0;
    }

    // 최대 감소하는 수인 9876543210이 1022번째 감소하는 수.
    if (N > 1022)
    {
        cout << -1;
        return 0;
    }
    
    queue<long long> q;

    for (long long i = 1; i < 10; i++)
    {
        q.push(i);
    }

    int cnt = 9;

    while (cnt <= N)
    {
        // 맨 앞의 수 추출
        long long num = q.front();
        q.pop();

        // 앞의 수의 마지막 수 추출
        long long last_digit = num % 10;

        // 마지막 수의 작은 수만 추가하도록 조치됨.
        for (long long i = 0; i < last_digit; i++)
        {
            q.push(num * 10 + i);
            cnt++;

            if (cnt == N)
            {
                cout << num * 10 + i;
                return 0;
            }
        }
    }

    return 0;
}
```

우선, 조사결과 최대 감소하는 수인 9876543210은 1022번째 감소하는 수이다. (N = 1022)<br/>

그리고 정답 제출코드를 출력하기 까지 두 차례의 오답이 있었다.<br/>

그 과정은 아래와 같이 서술한다.<br/>

<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

vector<int> v;

bool IsDescend(int a)
{
    bool ToReturn = false;
    if (a == 0)
        return true;
    else
    {
        v.push_back(a % 10);
        int len = v.size();
        for (int i = 0; i < len-1; ++i)
        {
            if (v[i] >= v[i+1])
                return false;
        }
        ToReturn = IsDescend(a / 10);
    }
    return ToReturn;
}

int main()
{
    int N = 0;
    cin >> N;

    int count = 0;
    long long number = 0;
    long long ans = -1;

    while (count <= N)
    {
        v = {};

        if (number > 9876543210)
        {
            ans = -1;
            break;
        }

        if (IsDescend(number))
        {
            ans = number;
            count++;
        }
        number++;
    }

    cout << ans;

    return 0;
}
```

다소 무식한 방법이었다..<br/>

수를 일일이 하나씩 검사하면서 감소하는 수인지를 판별해야 했기에 시간이 오래걸릴 것이다.<br/>

특히 N의 최대값이 1000000인 것을 생각하면 아찔하다.<br/>

실제로 N이 1000인 경우를 입력했더니 구동 시간이 10초가 넘었다.<br/>

이를 어떻게 개선할까 고민하였다.<br/>

솔직히 이번엔 챗 GPT에게 물어보았다.<br/>

그런데 큐를 사용하라는 답변이 돌아왔고, 아래는 답변 코드의 일부이다.<br/>

```
queue<long long> q;

    for (int i = 1; i < 10; i++)
    {
        q.push(i);
    }

    int cnt = 9;

    while (cnt <= N)
    {
        long long num = q.front();
        q.pop();

        int last_digit = num % 10;

        for (int i = 0; i < last_digit; i++)
        {
            q.push(num * 10 + i);
            cnt++;

            if (cnt == N)
            {
                cout << num * 10 + i;
                return 0;
            }
        }
    }

    cout << -1 << '\n';

    return 0;
```

어떻게 이게 작동하는 건지 궁금했는데, 작동이 잘 되는 것 같다.<br/>

```
// 맨 앞의 수 추출
long long num = q.front();
q.pop();

// 앞의 수의 마지막 수 추출
int last_digit = num % 10;

// 예를들어 num이 32일 경우 : last_digit = 1
// 320, 321이 새로 삽입됨.
// 마지막 수의 작은 수만 추가하도록 조치됨.
```

현재 수 * 10을 한 값에 마지막 digit보다 작은 값까지만 더해서 넣어준다.<br/>

맨 처음에 q를 생성하고 0부터 9까지 한자리수를 넣어놓았기 떄문에<br/>

작은 수부터 자연스럽게 삽입되게 된다.<br/>

이렇게 큐를 활용하니 바로 1초도 되지 않아서 결과가 출력이 되었다.<br/>
<br/>

## 오답제출코드(런타임 에러)<br/>
<br/>

```
#include <iostream>
#include <queue>

using namespace std;

int main()
{
    int N = 0;
    cin >> N;

    // 한자리 수는 그냥 출력하고 끝.
    if (N <= 9)
    {
        cout << N;
        return 0;
    }
    
    queue<long long> q;

    for (long long i = 1; i < 10; i++)
    {
        q.push(i);
    }

    int cnt = 9;

    while (cnt <= N)
    {
        // 맨 앞의 수 추출
        long long num = q.front();
        q.pop();

        // 앞의 수의 마지막 수 추출
        long long last_digit = num % 10;

        // 마지막 수의 작은 수만 추가하도록 조치됨.
        for (long long i = 0; i < last_digit; i++)
        {
            q.push(num * 10 + i);
            cnt++;

            if (cnt == N)
            {
                cout << num * 10 + i;
                return 0;
            }
        }
    }

    cout << -1 << '\n';

    return 0;
}
```
<br/>

그렇게 정답이 출력되는 줄 알았더니 이번엔 DoubleFree 런타임 에러가 떴다.<br/>

### 반례<br/>

```
500000

// 원하는 결과 : -1
// 출력 결과 : Error

```

정답코드에 상술하였듯이 최대 감소하는 수인 9876543210은 1022번째 N이 감소하는 수이다.<br/>

여기서 N이 1022를 초과하면 무한루프가 돌거나 메모리 초과가 뜨는 듯 하다.<br/>

그래서 할 수 없이 N이 1022를 초과하는 경우를 예외처리 해주었다.