---
layout: post
title:  "[백준] 1107번_리모컨 C++"
subtitle:   
date: 2023-03-15 10:04:30 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1107번_리모컨 C++<br/>
<br/>

완전 탐색을 활용하는 문제<br/>

for문과 백트래킹 두 가지 방법으로 풀어보았다.<br/>
<br/>

## 정답제출코드(for문 사용)<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <cmath>

using namespace std;

int n, m;
vector<bool> broken(10, false);

// 버튼을 누를 수 있는지 여부를 반환
bool possible(int num)
{
    if (num == 0)
    {
        if (broken[0])
            return false;
        else
            return true;
    }
    while (num > 0)
    {
        if (broken[num % 10])
            return false;
        num /= 10;
    }
    return true;
}

int main()
{
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);
    
    cin >> n;
    cin >> m;
    for (int i = 0; i < m; i++)
    {
        int btn;
        cin >> btn;
        broken[btn] = true;
    }

    int ans = abs(n - 100);
    for (int i = 0; i <= 1000000; i++)
    {
        if (possible(i))
        {
            int cnt = abs(i - n) + (int)to_string(i).length();
            ans = min(ans, cnt);
        }
    }
    cout << ans << '\n';

    return 0;
}
```
구글링을 해서 결국 풀었다.<br/>

[참고링크](https://tasddc.tistory.com/m/105)에 따르면 for문을 통해 0부터 1000000까지 완전히 탐색을 해야하는데<br/>

왜 1000000까지 탐색을 해야하나 했더니 반대방향에서 내려오는 부분을 고려해야한다는 것이다.<br/>

<Br/>

## 정답제출코드(백트래킹)<br/>
<br/>

백트래킹으로도 풀 수 있을 거 같아 백트래킹으로도 풀어보았다.<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

vector<int> Broken;
vector<int> CanPush;

int N, M, cur = 100;

int m_min(int a, int b)
{
    if (a > b)
        return b;
    else
        return a;
}

// a번이 부숴진 버튼인지 판별
bool IsBroken(int a)
{
    for (size_t i = 0; i < Broken.size(); ++i)
    {
        if (a == Broken[i])
            return true;
    }
    return false;
}

int backtrack(int depth, int Channel, int answer)
{
    if (depth >= 1)
        answer = m_min(answer, (int)abs(N - Channel) + depth);

    if (depth == 7)
        return answer;

    for (size_t i = 0; i < CanPush.size(); ++i)
    {
        int NextChannel = Channel * 10 + CanPush[i];
        answer = backtrack(depth+1, NextChannel, answer);
    }

    return answer;
}

int main()
{
    cin >> N;
    cin >> M;

    // M은 0이 아닐 경우만 입력받기
    if (M != 0)
    {
        for (int i = 0; i < M; ++i)
        {
            int _input;
            cin >> _input;

            Broken.push_back(_input);
        }
    }

    for (int i = 0; i < 10; ++i)
    {
        if (!IsBroken(i))
            CanPush.push_back(i);
    }

    // 에외처리 : N과 cur이 처음부터 같으면 누를 필요도 없다.
    if (N == cur)
    {
        cout << 0;
        return 0;
    }

    // 예외처리 : 버튼이 모두 부숴진경우 +과 -로 밖에 못움직인다.
    if (M == 10)
    {
        cout << (int)abs(N - cur);
        return 0;
    }

    int answer = backtrack(0, 0, (int)abs(N - cur));

    cout << answer;

    return 0;
}
```
<br/>
<br/>

### 예외처리<br/>
<br/>

1. 처음부터 현재 채널과 돌리고자 하는 채널이 같을 경우 0을 출력하고 리턴한다.<br/>

```
// N과 cur이 처음부터 같으면 누를 필요도 없다.
    if (N == cur)
    {
        cout << 0;
        return 0;
    }
```

2. 숫자 버튼이 모두 부숴진 경우 (M = 10인 경우)<br/>

```
// 예외처리 : 버튼이 모두 부숴진경우 +과 -로 밖에 못움직인다.
    if (M == 10)
    {
        cout << (int)abs(N - cur);
        return 0;
    }
```
+과 -로 밖에 못 움직이므로 N과 cur의 차이만 출력하고 리턴한다.<br/>
<br/>
for문과 백트래킹을 둘다 써본 결과 for문이 시간이 덜 걸렸다.<br/>

```
for문 사용시 : 메모리 2020KB / 소요시간 24ms

백트래킹 사용시 : 메모리 2020KB / 소요시간 48ms
```
<br/>

## 오답제출코드<br/>
<br/>

에.. 아래는 백트래킹을 사용하지 않고 for문을 사용해서 내가 삽질한 내용이다.<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

vector<int> Broken;
vector<int> ToPush;

int N, M, cur = 100, counter1 = 0, counter2 = 0;

// a번이 부숴진 버튼인지 판별
bool IsBroken(int a)
{
    for (int i = 0; i < M; ++i)
    {
        if (a == Broken[i])
            return true;
    }
    return false;
}

int main()
{
    cin >> N;
    cin >> M;

    // M은 0이 아닐 경우만 입력받기
    if (M != 0)
    {
        for (int i = 0; i < M; ++i)
        {
            int _input;
            cin >> _input;

            Broken.push_back(_input);
        }
    }

    // N과 cur이 처음부터 같으면 누를 필요도 없다.
    if (N == cur)
    {
        cout << 0;
        return 0;
    }

    // 각 자리수를 추출하는 과정

    int num = N;
    int digit = 100000;

    while (digit > num)
    {
        digit /= 10;
    }

    while (digit > 0)
    {
        ToPush.push_back(num / digit);

        num = num % digit;
        digit /= 10;
    }

    // 탐색 방법 1 가능한 버튼을 각 자리 수 별로 일치하도록 누르기

    int CanPush = 0;
    int len = ToPush.size();

    digit = 1;
    for (int i = 1; i < len; ++i)
        digit *= 10;

    for (int i = 0; i < len; ++i)
    {
        if (IsBroken(ToPush[i]))
        {
            // 리모컨이 부숴져 있을 경우
            int gap = 10;
            int PushCandidate = 0;
            for (int j = 0; j < 10; ++j)
            {
                if (!IsBroken(j))
                {
                    int gap2 = (int)abs(ToPush[i] - j);
                    if (gap2 < gap)
                    {
                        PushCandidate = j;
                        gap = gap2;
                    }       
                }
            }
            CanPush += digit * PushCandidate;
        }
        else
        {
            // 리모컨이 부숴지지 않았을 경우
            CanPush += digit * ToPush[i];
        }
        digit /= 10;
        counter1++;
    }
    counter1 += (int)abs(CanPush - N);


    // 탐색 방법2 일치여부 상관없이 최대한 가까운 수를 누르기

    CanPush = 0;
    digit = 1;
    for (int i = 1; i < len; ++i)
        digit *= 10;

    for (int i = 0; i < len; ++i)
    {
        int gap = 9999999;
        int PushCandidate = 0;
        for (int j = 0; j < 10; ++j)
        {
            if (!IsBroken(j))
            {
                int gap2 = (int)abs(N - ((j * digit) + CanPush));
                if (gap2 < gap)
                {
                    PushCandidate = j;
                    gap = gap2;
                }       
            }
        }
        CanPush += digit * PushCandidate;

        digit /= 10;
        counter2++;
    }
    counter2 += (int)abs(CanPush - N);

    // 더 작은 경우 출력
    if (counter2 > counter1)
        cout << counter1;
    else
        cout << counter2;

    return 0;
}
```
<br/>

### 각 자리수 추출<br/>
<br/>

```
// 각 자리수를 추출하는 과정

    int num = N;
    int digit = 100000;

    while (digit > num)
    {
        digit /= 10;
    }

    while (digit > 0)
    {
        ToPush.push_back(num / digit);

        num = num % digit;
        digit /= 10;
    }
```

N을 받은 뒤에 digit를 10^n으로 부여하여 각 자리수마다 추출하도록 구현하였다.<br/>

다음으로, 탐색을 하는 방법으로 두 가지를 사용하였다.<br/>
<br/>

### 탐색방법 1 :최대한 일치하는 버튼을 누르기<br/>
<Br/>

```
    // 탐색 방법 1 가능한 버튼을 각 자리 수 별로 일치하도록 누르기

    int CanPush = 0;
    int len = ToPush.size();

    digit = 1;
    for (int i = 1; i < len; ++i)
        digit *= 10;

    for (int i = 0; i < len; ++i)
    {
        if (IsBroken(ToPush[i]))
        {
            // 리모컨이 부숴져 있을 경우
            int gap = 10;
            int PushCandidate = 0;
            for (int j = 0; j < 10; ++j)
            {
                if (!IsBroken(j))
                {
                    int gap2 = (int)abs(ToPush[i] - j);
                    if (gap2 < gap)
                    {
                        PushCandidate = j;
                        gap = gap2;
                    }       
                }
            }
            CanPush += digit * PushCandidate;
        }
        else
        {
            // 리모컨이 부숴지지 않았을 경우
            CanPush += digit * ToPush[i];
        }
        digit /= 10;
        counter1++;
    }
    counter1 += (int)abs(CanPush - N);
```

각 자리수마다 비교하면서 누른다. 예를 들어, 5457을 누르고 싶다면 순서대로 5455를 누르도록 코드를 구현하였다.<br/>

대신에 이 코드의 결함은 80000을 누르고 싶다면 70000이 눌려서 77777을 눌러 카운터를 최소화 할 수 있음에도 불구하고 다른 값이 출력된다는 것이다.<br/>
<br/>

### 탐색방법 2 : 일치여부 상관없이 최대한 가까운 수를 누르기<br/>
<br/>

```
// 탐색 방법2 일치여부 상관없이 최대한 가까운 수를 누르기

    CanPush = 0;
    digit = 1;
    for (int i = 1; i < len; ++i)
        digit *= 10;

    for (int i = 0; i < len; ++i)
    {
        // 리모컨이 부숴져 있을 경우
        int gap = 9999999;
        int PushCandidate = 0;
        for (int j = 0; j < 10; ++j)
        {
            if (!IsBroken(j))
            {
                int gap2 = (int)abs(N - ((j * digit) + CanPush));
                if (gap2 < gap)
                {
                    PushCandidate = j;
                    gap = gap2;
                }       
            }
        }
        CanPush += digit * PushCandidate;

        digit /= 10;
        counter2++;
    }
    counter2 += (int)abs(CanPush - N);
```

80000을 누르고 싶다면 각 자리수를 누를때마다 N과 눌렀을 경우의 차이 값을 계산하면서 더 작은 값이 눌리도록 계산을 해준다.<br/>

예를 들어, 80000을 누르고 싶다면 위 코드의 결과로 77777이 눌린다.<br/>

하지만 이 코드의 단점은 5457을 누르고 싶을 경우 5500이 눌린다는 점이다.<br/>

그 과정은 아래와 같다.<br/>

- 5를 누른다 -> CanPush는 5000이 된다.
- 다음으로 5400과 5500의 5457과의 차이를 각각 계산한다.
- 5500이 5457에 더 가까우므로 5500을 누른다.

이렇게 원하는 값이 눌리지 않을 수 있다는 단점이 있다.<br/>
<br/>

### 더 작은 값 출력하기<br/>
<br/>

```
    // 더 작은 경우 출력
    if (counter2 > counter1)
        cout << counter1;
    else
        cout << counter2;
```

이렇게 두 가지 경우를 탐색하고 더 작은 카운터 값을 출력한다.<br/>
<br/>

하지만 이 코드는 정답이 아니었다..<br/>
<br/>

반례는 아래와 같았다.<br/>

### 반례1<br/>
<br/>

```
0
9
0 1 2 3 4 5 6 7 8

// 원하는 값 : 10
// 출력된 값 : 0
```

목표로 하는 값 N이 처음부터 0이면 어떤 버튼을 눌러야 하는지 목록에 삽입이 안된다.<br/>

### 반례2<br/>
<br/>

```
10
9
0 1 2 3 4 5 6 7 8

// 원하는 값 : 2
// 출력된 값 : 91
```

99를 누르고 (2번 누름) -를 89번 누르는 결과는 91이 출력된 것 같다.