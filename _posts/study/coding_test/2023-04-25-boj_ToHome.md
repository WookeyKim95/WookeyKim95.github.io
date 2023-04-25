---
layout: post
title:  "[백준] 1069_집으로 C++"
subtitle:   
date: 2023-04-25 09:12:42 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1069_집으로 C++<br/>
<br/>

스스로 아이디어를 내고 구현해보는 기하 애드혹 문제<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <cmath>

using namespace std;

double m_min(double a, double b)
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
    cout << fixed;

    int X, Y, D, T;
    cin >> X >> Y >> D >> T;

    double Distance = sqrt(X*X + Y*Y);
    double ans = 0, time1 = 0, time2 = 0, time3 = 10001, time4 = 10001, time5 = 0;

    
    // 걷기 뛰기 조합을 쓸 경우

    // (0,0)이전까지 뛸 경우
    int counter = Distance / D;
    double remain = Distance - counter * D;

    time1 = counter*T + remain;

    // (0,0) 이후까지 뛸 경우
    double counter2 = counter + 1;

    time2 = counter2 * D - Distance + counter2 * T;

    // 방향을 꺾어서 뛸 경우
    if (counter > 0)
        time3 = (double)(counter+1)*T;
    else
    {
        // 점프 두번만 할 경우
        if (Distance < D)
            time4 = T*2;
    }

    // 걷기만 할 경우
    time5 = Distance;

    ans = m_min(time5, m_min(time4, m_min(time3, m_min(time1, time2))));

    cout.precision(10);
    cout << ans;

    return 0; 
}
```

처음에는 쉽다고 생각했지만, 경우를 많이 따져야했다.<br/>

원을 이용해서 풀어보기도 할까 했지만 1시간을 고민한 끝에 결국 구글링을 했다.<br/>

그래서 찾은 [참고링크](https://mangu.tistory.com/15)의 글이 있다.<br/>

여기서 따지는 경우는 아래와 같다.<br/>

1. (0,0)이전까지 뛸 경우 = time1
2. (0,0)이후까지 뛸 경우 = time2
3. 방향을 두 번 꺾어서 뛸 경우 = time3
4. 점프만 두 번 할 경우 (단, Distance가 D보다 작을 때) = time4
5. 걷기만 할 경우 = time5

여기서 1, 2, 5번의 경우는 생각해냈지만<br/>

3, 4번의 경우를 생각하지 못했던 것 같다. 그래서 참고링크를 참고하였다.<br/>

휴.. 자신이 직접 경우의 수를 다 따지고 아이디어를 창조해야하는 애드혹 문제는 아직은 어려운 것 같다.<br/>