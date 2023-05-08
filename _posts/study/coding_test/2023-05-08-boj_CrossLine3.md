---
layout: post
title:  "[백준] 20149_선분 교차 3 C++"
subtitle:   
date: 2023-05-08 09:33:33 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 20149_선분 교차 3 C++<br/>
<br/>

기하학의 조건을 따지면서 구현하고 CCW를 활용하는 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <algorithm>

#define pdd pair<double, double>

using namespace std;

double X1, Y1, X2, Y2, X3, Y3, X4, Y4;

int CCW(pdd p1, pdd p2, pdd p3)
{
	double temp = p1.first * p2.second + p2.first * p3.second + p3.first * p1.second;

	temp = temp - p1.second * p2.first - p2.second * p3.first - p3.second * p1.first;

	if (temp > 0)
        return 1;
	else if (temp == 0)
        return 0;
	else if (temp < 0)
        return -1;
}

void FindInterSection(pdd A, pdd B, pdd C, pdd D)
{
    double px = (A.first * B.second - A.second * B.first) * (C.first - D.first) - 
                (A.first - B.first) * (C.first * D.second - C.second * D.first);
	double py = (A.first * B.second - A.second * B.first) * (C.second - D.second) -
                (A.second - B.second) * (C.first * D.second - C.second * D.first);
	double p = (A.first - B.first) * (C.second - D.second) - (A.second - B.second) * (C.first - D.first);

    if (p == 0) // 평행 시
	{
		// 교점이 하나일 때
		if (B == C && A <= C)
            cout << B.first << ' ' << B.second;
		else if (A == D && C <= A)
            cout << A.first << ' ' << A.second;
        
        // 선분이 겹칠 경우 패스
	}

	else // 교차할 때
	{
		double x = px / p;
		double y = py / p;

		cout.precision(10);
        cout << fixed;
		cout << x << ' ' << y;
	}
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> X1 >> Y1 >> X2 >> Y2;
    cin >> X3 >> Y3 >> X4 >> Y4;

    pdd A = {X1, Y1};
    pdd B = {X2, Y2};
    pdd C = {X3, Y3};
    pdd D = {X4, Y4};

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
        {
            cout << 1 << '\n';
            FindInterSection(A, B, C, D);
        }
        else
            cout << 0;
	}

    else 
    {
        if (abc * abd <= 0 && cda * cdb <= 0)
        {
            cout << 1 << '\n';
            FindInterSection(A, B, C, D);
        }
        else
            cout << 0;
    }

    return 0;
}
```

CCW를 모르는 상태에서 풀려니 코드가 꽤 복잡했다.<br/>

CCW를 활용해서 푸는 과정은 [이 분의 블로그](https://cocoon1787.tistory.com/489)를 참고하였다.<br/>

여기서 Y절편, 기울기를 구할 필요도 없이 교점을 한큐에 구하는 방법도 배워갔다.<br/>

3일 동안 아래의 오답제출코드를 가지고 고민했지만 도대체 어디서 틀렸는지 모르겠어서 결국 구글링했다.<br/>

다들 CCW을 사용하는 방향으로 나와는 전혀 다른 방법으로 푸셨던데<br/>

내가 했던 풀이를 보수하지 못해서 아쉬웠던 것 같다.<br/>
<br/>

## 오답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

long long X1, Y1, X2, Y2, X3, Y3, X4, Y4;
double Grad1, Grad2;
double Y_inter1, Y_inter2;
double AnsX, AnsY;

bool IsSame(pair<long long, long long> a, pair<long long, long long> b)
{
    return a == b;
}

long long m_max(long long a, long long b)
{
    if (a > b)
        return a;
    else
        return b;
}

long long m_min(long long a, long long b)
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
        Grad2 = (double)(Y4 - Y3) / (double)(X4 - X3);
        Y_inter2 = (double)Y4 - (Grad2 * (double)X4);

        if (((Grad2 * (double)X1 + Y_inter2) - (double)Y1) * ((Grad2 * (double)X2 + Y_inter2) - (double)Y2) > 0.f)
            NotCross();

        else
        {
            AnsX = (double)X2;
            AnsY = Grad2 * AnsX + Y_inter2;

            cout << 1 << '\n';

            cout.precision(10);
            cout << fixed;
            cout << AnsX << ' ' << AnsY;
            return 0;
        }
    }

    // 2. L2가 수직이면서 L1이 수직이 아닐 때
    else if (X4 - X3 == 0 && X2 - X1 != 0)
    {
        Grad1 = (double)(Y2 - Y1) / (double)(X2 - X1);
        Y_inter1 = (double)Y2 - (Grad1 * (double)X2);

        if (((Grad1 * (double)X3 + Y_inter1) - (double)Y3) * ((Grad1 * (double)X4 + Y_inter1) - (double)Y4) > 0.f)
            NotCross();

        else
        {
            AnsX = (double)X4;
            AnsY = Grad1 * (double)AnsX + Y_inter1;

            cout << 1 << '\n';

            cout.precision(10);
            cout << fixed;
            cout << AnsX << ' ' << AnsY;
            return 0;
        }
    }

    // 3. 둘다 수직일 때 (이 시점에서 X값이 다른 경우와 안 만나는 경우는 이미 걸러짐.)
    else if (X4 - X3 == 0 && X2 - X1 == 0)
    {
        cout << 1 << '\n';
        if (m_min(Y1, Y2) == m_max(Y3, Y4))
            cout << X1 << ' ' << m_max(Y3, Y4);

        else if (m_max(Y1, Y2) == m_min(Y3, Y4))
            cout << X1 << ' ' << m_min(Y3, Y4);

        return 0;
    }

    // 선분 기울기 구하기
    Grad1 = (double)(Y2 - Y1) / (double)(X2 - X1);
    Grad2 = (double)(Y4 - Y3) / (double)(X4 - X3);

    // 각 직선의 Y절편 구하기
    Y_inter1 = (double)Y2 - (Grad1 * (double)X2);
    Y_inter2 = (double)Y4 - (Grad2 * (double)X4);

    // 교차하지 않는 경우 2 : 어떤 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있을 때
    if ((double)Y3 > (Grad1 * (double)X3 + Y_inter1) &&
        (double)Y4 > (Grad1 * (double)X4 + Y_inter1))
        NotCross();

    if ((double)Y3 < (Grad1 * (double)X3 + Y_inter1) && 
        (double)Y4 < (Grad1 * (double)X4 + Y_inter1))
        NotCross();
    
    if ((double)Y1 > (Grad2 * (double)X1 + Y_inter2) && 
        (double)Y2 > (Grad2 * (double)X2 + Y_inter2))
        NotCross();

    if ((double)Y1 < (Grad2 * (double)X1 + Y_inter2) && 
        (double)Y2 < (Grad2 * (double)X2 + Y_inter2))
        NotCross();

    // 여기까지 온 경우는 교차하는 경우이다.
    // 교차하는 경우 2가지 : 겹칠 때, 한 점에서 교차할 때

    // 선분이 겹치는 경우
    if (Grad1 == Grad2 && Y_inter1 == Y_inter2)
    {
        cout << 1 << '\n';

        // 같은 선 상이라도 한 점에서 이어지는 경우에는 출력
        if (m_min(X1, X2) == m_max(X3, X4) && m_min(Y1, Y2) == m_max(Y3, Y4))
            cout << m_min(X1, X2) << ' ' << m_min(Y1, Y2);
        
        else if (m_max(X1, X2) == m_min(X3, X4) && m_max(Y1, Y2) == m_min(Y3, Y4))
            cout << m_max(X1, X2) << ' ' << m_max(Y1, Y2);
        
        // 그렇지 않은 경우 겹치는 것이므로 패스.
        return 0;
    }

    // 선분이 겹치지 않으면서 한 점에서 교차할 때

    // 같은 점이 있을 경우
    if (IsSame({X1, Y1}, {X3, Y3}))
    {
        cout << 1 << '\n';
        cout << X1 << ' ' << Y1;
        return 0;
    }

    if (IsSame({X2, Y2}, {X3, Y3}))
    {
        cout << 1 << '\n';
        cout << X2 << ' ' << Y2;
        return 0;
    }

    if (IsSame({X1, Y1}, {X4, Y4}))
    {
        cout << 1 << '\n';
        cout << X1 << ' ' << Y1;
        return 0;
    }

    if (IsSame({X2, Y2}, {X4, Y4}))
    {
        cout << 1 << '\n';
        cout << X2 << ' ' << Y2;
        return 0;
    }

    // 여기까지 온 경우는 일반적인 한 점에서 교차하는 경우이다.
    // 그 때 점을 구하기.

    AnsX = -1 * (Y_inter1 - Y_inter2) / (Grad1 - Grad2);
    AnsY = Grad1 * AnsX + Y_inter1;

    cout << 1 << '\n';
    cout.precision(10);
    cout << fixed;
    cout << AnsX << ' ' << AnsY;

    return 0;
}
```

<br/>

만나지 않는 경우를 모두 따지자.<br/>

- 한 선분의 최대 X값, Y값이 다른 선분의 최소 X값, Y값보다 작은 경우
- 어떤 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있는 경우

<br/>

### 한 선분의 최대 X값, Y값이 다른 선분의 최소 X값, Y값보다 작은 경우<br/>
<br/>

그림과 같이 한 선분의 최대 X값이나 Y값이 다른 선분의 최소 X값이나 Y값보다 작으면 만나지 않는다.<br/>

![그림1](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2023-05-08_1.jpg?raw=true)

<br/>

### 어떤 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있는 경우<br/>
<br/>

그림과 같이 한 선분의 두 점이 모두 다른 선분이 가른 두 영역 중 같은 영역에 있는 경우,<br/>

두 선분은 만나지 않는다.<br/>

![그림2](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2023-05-08_2.jpg?raw=true)

<br/>

<br/>

위 두 경우 중 하나라도 해당된다면 만나지 않는다.<br/>

그리고 만나지 않는 경우를 모두 통과할 경우 만나는 것으로 판정한다.<br/>

그리고 주의할 점은 만나는 경우도 여러가지가 있다는 것이다.<br/>

- 겹치는 경우
- 같은 점이 경우
- 이외의 보통 한 점에서 만나는 경우

우선, 선분이 겹치는지 여부를 먼저 판정해준다.<br/>

여기서, 주의할 점은 겹치는 점이 2개 이상인지를 판단해주어야 한다.<br/>

같은 직선상에 있더라도 한 점에서 이어질 수 있기 때문!

```
// 선분이 겹치는 경우
if (Grad1 == Grad2 && Y_inter1 == Y_inter2)
{
    cout << 1 << '\n';

    // 같은 선 상이라도 한 점에서 이어지는 경우에는 출력
    if (m_min(X1, X2) == m_max(X3, X4))
        cout << m_min(X1, X2) << ' ' << m_min(Y1, Y2);
    
    else if (m_max(X1, X2) == m_min(X3, X4))
        cout << m_max(X1, X2) << ' ' << m_max(Y1, Y2);
    
    // 그렇지 않은 경우 겹치는 것이므로 패스.
    return 0;
}
```

다음으로, 위의 경우를 통과한 경우 중에서<br/>

주어진 점들 중에서 같은 점이 있다면 무조건 만난다.<br/>

그래서 그 점에서 만나는 것으로 판정한다.<br/>

그 다음에 이 경우를 통과하고 나서야 일반적으로 한 점에서 교차하는 경우이다.<br/>

이 경우는 연립방정식을 사용해서 구해준다.<br/>

![그림3](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/coding_test/2023-05-08_3.jpg?raw=true)

<br/>

위 식을 코드로 구현하면 아래와 같다.<br/>

```
    AnsX = -1 * (Y_inter1 - Y_inter2) / (Grad1 - Grad2);
    AnsY = Grad1 * AnsX + Y_inter1;
```

하지만 50%에서 틀렸습니다 판정이 떴고, 아쉽게도 이 오답 코드는 더 이상 제출하지 않기로 하였다.<br/>

대신 기록으로는 남겨둬야지.