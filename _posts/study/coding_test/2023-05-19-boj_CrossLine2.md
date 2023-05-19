---
layout: post
title:  "[백준] 17387_선분 교차 2 C++"
subtitle:   
date: 2023-05-19 09:39:53 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17387_선분 교차 2 C++<br/>
<br/>

기하학의 조건을 따지면서 구현하는 문제, CCW에 대해 배워보자<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <algorithm>

#define ll long long

using namespace std;

long long X1, Y1, X2, Y2, X3, Y3, X4, Y4;

int CCW(pair<ll, ll> p1, pair<ll, ll> p2, pair<ll, ll> p3)
{
	long long temp = p1.first * p2.second + p2.first * p3.second + p3.first * p1.second;

	temp = temp - p1.second * p2.first - p2.second * p3.first - p3.second * p1.first;

	if (temp > 0)
        return 1;
	else if (temp == 0)
        return 0;
	else if (temp < 0)
        return -1;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> X1 >> Y1 >> X2 >> Y2;
    cin >> X3 >> Y3 >> X4 >> Y4;

    pair<int, int> A = {X1, Y1};
    pair<int, int> B = {X2, Y2};
    pair<int, int> C = {X3, Y3};
    pair<int, int> D = {X4, Y4};

	int abc = CCW(A, B, C);
	int abd = CCW(A, B, D);
	int cda = CCW(C, D, A);
	int cdb = CCW(C, D, B);
    
    if (abc * abd == 0 && cda * cdb == 0)
	{
		if (A > B)
            swap(A, B);
		if (C > D)
            swap(C, D);

		if (A <= D && C <= B)
			cout << 1;
		else
			cout << 0;

		return 0;
	}
	

	if (abc * abd <= 0 && cda * cdb <= 0)
		cout << 1;
	else
		cout << 0;

    return 0;
}

```

CCW에 대해서 알아보기 위해 [참고링크](https://imucoding.tistory.com/1060)를 참조하여 풀었다.<br/>

우선 CCW가 무엇인지는 [이쪽 참고링크](https://degurii.tistory.com/47)를 참고하였다.<br/>

세 점의 방향관계가 반시계 방향인지, 시계방향인지를 판별하는 방법이다.<br/>

기하에서 사칙연산처럼 쓰이는 알고리즘이라고..<br/>

```
struct Point
{
	int x = 0;
    int y = 0;
};

int ccw(Point A, Point B, Point C)
{
	return (B.x - A.x)*(C.y - A.y) - (C.x - A.x)*(B.y - A.y);
}
```

<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

double X1, Y1, X2, Y2, X3, Y3, X4, Y4;
double Grad1, Grad2;
double Y_inter1, Y_inter2;

bool IsSame(pair<double, double> a, pair<double, double> b)
{
    return a == b;
}

bool IsSamePoint()
{
    if (IsSame({X1, Y1}, {X3, Y3}))
        return true;
    if (IsSame({X1, Y1}, {X4, Y4}))
        return true;
    if (IsSame({X2, Y2}, {X3, Y3}))
        return true;
    if (IsSame({X2, Y2}, {X4, Y4}))
        return true;
    return false;
}

double m_max(double a, double b)
{
    if (a > b)
        return a;
    else
        return b;
}

double m_min(double a, double b)
{
    if (a < b)
        return a;
    else
        return b;
}

void NotCross()
{
    cout << 0;
    exit(0);
}

void Cross()
{
    cout << 1;
    exit(0);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> X1 >> Y1 >> X2 >> Y2;
    cin >> X3 >> Y3 >> X4 >> Y4;

    // 교차하지 않는 경우 1 : 범위가 서로 다를 때
    if ((m_max(X1, X2) < m_min(X3, X4)))
        NotCross();

    if ((m_max(X3, X4) < m_min(X1, X2)))
        NotCross();

    if ((m_max(Y1, Y2) < m_min(Y3, Y4)))
        NotCross();

    if ((m_max(Y3, Y4) < m_min(Y1, Y2)))
        NotCross();

    // 기울기를 구하기 전 체크 사항
    // 선분의 기울기가 x축과 수직인경우

    // 1. L1이 수직이면서 L2가 수직이 아닐 때
    if (X2 - X1 == 0 && X4 - X3 != 0)
    {
        Grad2 = (Y4 - Y3) / (X4 - X3);
        Y_inter2 = Y4 - (Grad2 * X4);

        if (((Grad2 * X1 + Y_inter2) - Y1) * ((Grad2 * X2 + Y_inter2) - Y2) > 0.f)
            NotCross();

        else
            Cross();
    }

    // 2. L2가 수직이면서 L1이 수직이 아닐 때
    else if (X4 - X3 == 0 && X2 - X1 != 0)
    {
        Grad1 = (Y2 - Y1) / (X2 - X1);
        Y_inter1 = Y2 - (Grad1 * X2);

        if (((Grad1 * X3 + Y_inter1) - Y3) * ((Grad1 * X4 + Y_inter1) - Y4) > 0.f)
            NotCross();

        else
            Cross();
    }

    // 3. 둘다 수직일 때 (이 시점에서 X값이 다른 경우와 안 만나는 경우는 이미 걸러짐.)
    else if (X4 - X3 == 0 && X2 - X1 == 0)
        Cross();
    
    // 선분 기울기 구하기
    Grad1 = (Y2 - Y1) / (X2 - X1);
    Grad2 = (Y4 - Y3) / (X4 - X3);

    // 각 직선의 Y절편 구하기
    Y_inter1 = Y2 - (Grad1 * X2);
    Y_inter2 = Y4 - (Grad2 * X4);

    // 교차하지 않는 경우 2 : 어떤 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있을 때

    if ((Y3 - (Grad1 * X3 + Y_inter1)) * (Y4 - (Grad1 * X4 + Y_inter1)) > 0.f
        && (!IsSamePoint()))
        NotCross();

    if ((Y2 - (Grad2 * X2 + Y_inter2)) * (Y1 - (Grad2 * X1 + Y_inter2)) > 0.f
        && (!IsSamePoint()))
        NotCross();

    // 여기까지 온 경우는 교차하는 경우이다.
    // 교차하는 경우 2가지 : 겹칠 때, 한 점에서 교차할 때

    // 선분이 겹치는 경우
    if (Grad1 == Grad2 && Y_inter1 == Y_inter2)
        Cross();

    // 선분이 겹치지 않으면서 한 점에서 교차할 때

    // 같은 점이 있을 경우
    if (IsSame({X1, Y1}, {X3, Y3}))
        Cross();

    if (IsSame({X2, Y2}, {X3, Y3}))
        Cross();

    if (IsSame({X1, Y1}, {X4, Y4}))
        Cross();

    if (IsSame({X2, Y2}, {X4, Y4}))
        Cross();

    // 여기까지 온 경우는 일반적인 한 점에서 교차하는 경우이다.

    cout << 1;

    return 0;
}
```

<br/>

CCW를 모를적에 이렇게 풀었다.<br/>

만나지 않는 경우를 모두 따지고, 그렇지 않은 경우에는 모두 만나는 경우로 출력하도록 했다.<br/>

하지만 내가 간과한 부분이 있었나보다.<br/>

만나지 않는 경우를 모두 따지자.<br/>

- 한 선분의 최대 X값, Y값이 다른 선분의 최소 X값, Y값보다 작은 경우
- 어떤 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있는 경우

<br/>

### 한 선분의 최대 X값, Y값이 다른 선분의 최소 X값, Y값보다 작은 경우<br/>
<br/>

그림과 같이 한 선분의 최대 X값이나 Y값이 다른 선분의 최소 X값이나 Y값보다 작으면 만나지 않는다.<br/>

![그림1](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2023-05-06_1.jpg?raw=true)

<br/>

### 어떤 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있는 경우<br/>
<br/>

그림과 같이 한 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있는 경우,<br/>

두 선분은 만나지 않는다.<br/>

![그림2](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2023-05-06_2.jpg?raw=true)

<br/>

<br/>

위 두 경우 중 하나라도 해당된다면 만나지 않는다.<br/>

그리고 만나지 않는 경우를 모두 통과할 경우 만나는 것으로 판정한다.<br/>

하지만 결과는 50%에서 틀렸습니다가 출력되었다.<br/>

구글링을 해보니 다들 CCW로 풀으셔서 도대체 내가 어느 부분을 간과한것인지는 찾지못했다.<br/>

챗 GPT도 모르겠다고 하고 (투덜)<br/>

그래서 CCW를 사용해서 푸는 것으로 방향을 바꿨다.<br/>