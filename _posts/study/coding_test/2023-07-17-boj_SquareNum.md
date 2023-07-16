---
layout: post
title:  "[백준] 1540_정사각형의 개수 C++"
subtitle:   
date: 2023-07-17 07:29:37 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1540_정사각형의 개수 C++<br/>
<br/>

수학적 규칙을 찾아내는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <cmath>
#include <cstring>

using namespace std;

int N;
int Point[1000001];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    memset(Point, 0, sizeof(Point));

    cin >> N;

    if (N == 0 || N == 1)
    {
        cout << 0;
        return 0;
    }

    for (int i = 2; i <= 1000; ++i)
        Point[i*i] = Point[(i-1)*(i-1)] + (i-1)*(i-1);

    int Idx = 0, Plus = 2;

    for (int i = 5; i <= 1000000; ++i)
    {
        if (Point[i] != 0)
        {
            Plus = (int)sqrt(i);
            Idx = 0;
            continue;
        }
        if (Idx == Plus)
        {
            Point[i] = Point[i-1];
            Idx = 1;
        }
        else
        {
            Point[i] = Point[i-1] + Idx;
            Idx++;
        }
    }

    cout << Point[N];

    return 0;
}
```

처음에 생각했던 코드는 아래와 같았다.<br/>

```
#include <iostream>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    int Point[1000001];

    Point[0] = 0;
    Point[1] = 0;
    Point[2] = 0;
    Point[3] = 0;
    Point[4] = 1;

    for (int i = 5; i <= 1000000; i += 2)
    {
        Point[i] = Point[i-1];
        Point[i+1] = Point[i-1] + 1;
    }

    cout << Point[N];

    return 0;
}
```

우선, 변이 최대한 

정사각형의 길이가 1인 직사각형을 센다면 위와 같아지긴 한다.<br/>

하지만 내가 간과한 것이 있었다.<br/>

길이가 2 이상인 정사각형을 어떻게 세야하지??<br/>

테스트케이스에서 점이 16개 인 경우의 답이 14라고 나왔는데,<br/>

- 변의 길이가 1인 정사각형 : 9개
- 변의 길이가 2인 정사각형 : 4개
- 변의 길이가 3인 정사각형 : 1개

이렇게 구성이 된다.<br/>

그래서 14개이다.<br/>

즉, 길이가 1인 정사각형만이 아니라, 길이가 2 이상인 정사각형도 세어줘야 하는 것이다.<br/>

<br/>

아이디어가 떠오르지 않아서 [이분의 글](https://ongveloper.tistory.com/337)을 참고하였다.<br/>

아하! 이분은 규칙을 찾아내셨다.<br/>

결론을 쓰자면 <br/>

- 점을 시계방향으로 우측부터 찍는다고 하자.
- 우측, 아래에 점을 찍을 동안 점화식은 Point[n+i] = Point[n+i-1] + i-1이다.<br/>
- 대각선 방향으로 점을 찍었을 때에는 정사각형의 개수가 증가하지 않는다.<br/>

그래서 이런 코드가 나오는 것이다.<br/>

```
for (int i = 5; i <= 1000000; ++i)
{
if (Point[i] != 0)
{
    Plus = (int)sqrt(i);
    Idx = 0;
    continue;
}
if (Idx == Plus)
{
    Point[i] = Point[i-1];
    Idx = 1;
}
else
{
    Point[i] = Point[i-1] + Idx;
    Idx++;
}
}
```

여기서 점화식의 n의 역할을 i, 그리고 점화식의 i 역할을 Idx가 하는 것이다!<br/>

Plus는 대각선 지점에 점을 찍었는가를 알려주기 위한 것이다.<br/>
