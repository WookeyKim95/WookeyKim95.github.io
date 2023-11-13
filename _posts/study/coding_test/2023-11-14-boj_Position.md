---
layout: post
title:  "[백준] 3980_선발 명단 C++"
subtitle:   
date: 2023-11-14 07:22:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 3980_선발 명단 C++<br/>
<br/>

오랜만에 풀어보는 백트래킹 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cstring>

using namespace std;

vector<vector<int>> vec;
bool used[11];
int Ans = 0;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

void backtrack(int depth, int Sum)
{
    if (depth == 11)
    {
        Ans = m_max(Ans, Sum);
        return;
    }

    for (int i = 0; i < 11; ++i)
    {
        if (!used[i] && vec[depth][i] != 0)
        {
            used[i] = true;
            backtrack(depth+1, Sum + vec[depth][i]);
            used[i] = false;
        }
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int T;
    cin >> T;
    
    for (int tc = 0; tc < T; ++tc)
    {
        vec.clear();
        memset(used, false, sizeof(used));
        Ans = 0;

        for (int i = 0; i < 11; ++i)
        {
            vector<int> LineUp;
            for (int j = 0; j < 11; ++j)
            {
                int _input;
                cin >> _input;
                LineUp.push_back(_input);
            }
            vec.push_back(LineUp);
        }

        backtrack(0, 0);

        cout << Ans << '\n';
    }

    return 0;
}
```
<br/>

## 처음 생각했던 코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

bool cmp(pair<int, int> a, pair<int, int> b)
{
    return a.first > b.first;
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int T;
    cin >> T;
    
    for (int tc = 0; tc < T; ++tc)
    {
        int Sum = 0;
        bool used[11];
        for (int i = 0; i < 11; ++i)
        {
            vector<pair<int, int>> LineUp;
            for (int j = 0; j < 11; ++j)
            {
                int _input;
                cin >> _input;
                LineUp.push_back({_input, j});
            }

            sort(LineUp.begin(), LineUp.end(), cmp);

            for (int j = 0; j < 11; ++j)
            {
                if (!used[LineUp[j].second])
                {
                    Sum += LineUp[j].first;
                    used[LineUp[j].second] = true;
                    break;
                }
            }
        }

        cout << Sum << '\n';
    }

    return 0;
}
```

제일 높은 값을 합쳐서 뽑는거니까 그냥 각 줄 마다 정렬한 다음에 제일 높은 값 뽑으면 되는 것 아닌가?<br/>

그러고 나서 사용한 포지션은 방문 처리 해서 중복 제거 하면 되는 것 아닌가?<br/>

생각했다.<br/>

하지만 아쉽게도 그것은 아니었다.<br/>

샘플 테스트 케이스 부터 반례가 발생했다.<br/>

```
1
100 0 0 0 0 0 0 0 0 0 0
0 80 70 70 60 0 0 0 0 0 0
0 40 90 90 40 0 0 0 0 0 0
0 40 85 85 33 0 0 0 0 0 0
0 70 60 60 85 0 0 0 0 0 0
0 0 0 0 0 95 70 60 60 0 0
0 45 0 0 0 80 90 50 70 0 0
0 0 0 0 0 40 90 90 40 70 0
0 0 0 0 0 0 50 70 85 50 0
0 0 0 0 0 0 66 60 0 80 80
0 0 0 0 0 0 50 50 0 90 88

// 희망 출력 값 : 970
// 실제 출력 값 : 968
```

왜 그런가 봤더니, 10번과 11번 줄을 주목해보자.<br/>

10번 줄에서는 11번 선수를 배치하고, 11번 줄에서 10번 선수를 배치하면<br/>

80 + 90으로 더 높은 값을 얻을 수 있다.<br/>

하지만 처음 생각했던 코드대로 라면 10번 선수를 우선적으로 배치하기 때문에<br/>

11번 줄에서는 자동으로 11번 선수가 배치 되어 80 + 88의 값을 얻는 것이다.<br/>

이렇기 때문에 오답이 출력되었다.<br/>

결국, 이 방법은 적절하지 않은 방법이라고 생각했고,<br/>

정답제출코드와 같이 변경하였다.<br/>

그렇게 했더니 값이 옮바르게 출력되었고, 정답처리 되었다!<br/>