---
layout: post
title:  "[백준] 1301번_비즈 공예 C++"
subtitle:   
date: 2023-04-13 10:28:17 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1301번_비즈 공예 C++<br/>
<br/>

DP를 활용하는 문제.<br/>
<br/>

## 정답제출코드<br>
<br/>

```
#include <iostream>
#include <cstring>

using namespace std;

int N, BizSum = 0;
int Biz[6] = {0, };
long long BizCount[11][11][11][11][11][6][6];

long long recur(int depth, int prev2, int prev1)
{
    long long& ToReturn = BizCount[Biz[1]][Biz[2]][Biz[3]][Biz[4]][Biz[5]][prev2][prev1];
    if (depth == BizSum)
        return 1;

    if (ToReturn != -1)
        return ToReturn;

    ToReturn = 0;
    for (int i = 1; i <= N; ++i)
    {            
        if (Biz[i] > 0 && prev2 != i && prev1 != i)
        {
            --Biz[i];
            ToReturn += recur(depth+1, prev1, i);
            ++Biz[i];
        }
    }
    return ToReturn;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    for (int i = 1; i <= N; ++i)
    {
        int _input;
        cin >> _input;
        Biz[i] = _input;
        BizSum += _input;
    }

    memset(BizCount, -1, sizeof(BizCount));    
    long long ans = recur(0, 0, 0);
    cout << ans;

    return 0;
}
```

[참고링크](https://gusdnr69.tistory.com/188)를 참고해서 풀었다.<br/>

오답제출코드랑 로직 자체는 그렇게 차이가 크게 나진 않지만<br/>

**일반적인 long long이 아니라 long long& 으로 참조자를 사용해야 했기 때문에**<br/>

vector보다는 일반 배열을 사용해야하는게 문제였던 것 같다.<br/>

참고링크에 따르면 무려 7차원 배열을 통해서<br/>

각 구슬이 남아있는 경우와 이어져있는 경우를 저장해야 한다.<br/>

그렇게해서 배열로부터 경우의 수를 추출해서 답을 내야하는 문제였다.<br/>

참조자의 속도측면에서의 위력을 실감할 수 있었던 문제였다.<br/>
<br/>


## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N, BizCount = 0, ans = 0;
int Necklace[35];
vector<int> Biz;

void backtrack(int depth)
{
    if (depth == BizCount)
    {
        ans++;
        return;
    }

    for (int i = 0; i < N; ++i)
    {            
        if (depth == 0 ||
        (depth >= 2 && Biz[i] > 0 && Necklace[depth-1] != i && Necklace[depth-2] != i))
        {
            --Biz[i];
            Necklace[depth] = i;
            backtrack(depth+1);
            Necklace[depth] = -1;
            ++Biz[i];
        }
        else if (depth == 1 && Biz[i] > 0 && Necklace[depth-1] != i)
        {
            --Biz[i];
            Necklace[depth] = i;
            backtrack(depth+1);
            Necklace[depth] = -1;
            ++Biz[i];
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;
    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;
        Biz.push_back(_input);
        BizCount += _input;
    }

    if (N == 1)
    {
        cout << 0;
        return 0;
    }

    for (int i = 0; i < BizCount; ++i)
        Necklace[i] = -1;

    backtrack(0);

    cout << ans;

    return 0;
}
```

놀랐었던 점은 문제만 읽어보면 백트래킹인데 알고리즘 분류를 보니 DP로 분류되었다는 점이다.<br/>

우선 나는 백트래킹을 사용해서 구현하였다.<br/>

천만 다행이게도, 목걸이는 직선형이기 때문에 마지막 구슬과 첫번째 구슬이 겹치는지 여부는 상관이 없다.<br/>

depth = 0이거나 depth가 2이상이면서 두칸 내에 같은 종류의 구슬이 없다면<br/>

목걸이에 구슬을 넣고 다음 경우를 탐색하도록 하였다.<br/>

그렇게 해서 목걸이가 모두 꽂히면 (depth == BizCount)이면 경우를 1 더하고 return 시켰다.<br/>

이렇게 해서 모든 경우를 탐색해서 ans를 출력하도록 구현하였다.<br/>

그러나 시간초과가 발생하였다.<br/>

구글링을 해서 참고를 해도 로직 자체는 맞는 것 같다. 그럼 시간 문제라는건데<br/>

이것을 어떻게 수정할 수 있을까?<br/>

결국 [참고링크](https://gusdnr69.tistory.com/188)를 참고해서 풀었다!<br/>

그렇게 해서 완성한 것이 위의 코드이다!<br/>