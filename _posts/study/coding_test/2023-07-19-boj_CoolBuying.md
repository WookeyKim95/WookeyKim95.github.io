---
layout: post
title:  "[백준] 1214_쿨한 물건 구매 C++"
subtitle:   
date: 2023-07-19 07:59:26 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1214_쿨한 물건 구매 C++<br/>
<br/>

수학 정수론 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>

using namespace std;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int m_min(int a, int b)
{
    if (a < b)
        return a;
    else
        return b;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int D, P, Q;
    cin >> D >> P >> Q;

    int bigger = m_max(P, Q);
    int smaller = m_min(P, Q);

    if (D % P == 0 || D % Q == 0)
    {
        cout << D;
        return 0;
    }

    int ans = (D/bigger) * bigger + bigger;
    int tmp = ans;

    for (int i = 1; i <= tmp/bigger; ++i)
    {
        int temp = tmp-bigger*i;
        
        if ((D - temp) % smaller == 0)
        {
            cout << D;
            return 0;
        }
        else
            temp += ((D-temp)/smaller) * smaller + smaller;
        
        if (ans == temp)
            break;
        ans = m_min(ans, temp);
    }

    cout << ans;

    return 0;
}
```

처음 문제를 접했을 때 생각 : 이게 왜 플레지?<br/>

풀면서 든 생각 : 으아아 잘못했어요.<br/>

<br/>

처음에 시도했던 접근 방법은 P와 Q중 큰 수를 구해서<br/>

되도록이면 큰 수를 먼저 내는 것이었다.<br/>

그리고 금액에 도달하면 그 때부터 작은 수를 내는 것이다.<br/>

하지만 이 문제의 목적은 지폐를 적게 내는 것이 아니라<br/>

총 합을 가장 적게 하는 것이기 때문에<br/>

오히려 작은 수를 더 많이 낼 때 이득인 상황이 발생하기도 한다.<br/>

이 점이 힘들었던 것 같다.<br/>

일일이 경우의 수를 따지려고 해도 제한시간에 걸려 시간초과가 날 것 같았다.<br/>

<br/>

결국 [참고링크](https://alswnsdl.tistory.com/21)를 참고하여 풀었고, 이 코드를 이해해보기로 했다.<br/>

```
int ans = (D/bigger) * bigger + bigger;
int tmp = ans;
```

우선, ans에다가 큰 수의 지폐만을 냈을 경우를 넣어준다.<br/>

그리고 tmp는 ans 초기 값을 보관하는 상수 용도로 사용한다.<br/>

```
for (int i = 1; i <= tmp/bigger; ++i)
{
    int temp = tmp-bigger*i;
    
    if ((D - temp) % smaller == 0)
    {
        cout << D;
        return 0;
    }
    else
        temp += ((D-temp)/smaller) * smaller + smaller;
    
    if (ans == temp)
        break;
    ans = m_min(ans, temp);
}
```

이 코드를 이해하는 것이 관건이었던 것 같다.<br/>

temp는 ans의 초기값에서 큰 수의 지폐를 덜 냈을 경우의 수이다.<br/>

이 때, (D - 덜낸 정도) 가 작은 수와 나누어 떨어지면 곧 바로 D를 제출한다.<br/>

그렇지 않다면 작은 수의 지폐를 더 낸다.<br/>

그리고 그 결과가 설정해준 ans 값과 일치하면 break,<br/>

그렇지 않다면 temp값과 ans값을 비교해서 작은 값을 다시 ans로 설정해준다.<br/>

그렇게 반복해서 ans값을 출력하면 되는 것.<br/>

<br/>

나누어 떨어지는 경우인가를 조사해주고, 그렇지 않다면 최선의 값을 도출하는 것이<br/>

이번 문제의 목표였던 것 같다.<br/>

while문을 쓸 수 있을까? 가능할 것이라 생각이 들지만<br/>

for문에 비해서는 코드가 난잡해질 것 같다는 생각도 든다.<br/>