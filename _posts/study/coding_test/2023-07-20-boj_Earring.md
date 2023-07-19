---
layout: post
title:  "[백준] 1380_귀걸이 C++"
subtitle:   
date: 2023-07-20 07:35:59 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1380_귀걸이 C++<br/>
<br/>

입력 받는 부분에서 애먹었던 구현문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int count = 1;
    while (true)
    {
        int N;
        cin >> N;
        cin.ignore();

        if (N == 0)
            break;
        
        vector<string> Students;
        vector<pair<bool, bool>> V;

        string _input;
        for (int i = 0; i < N; ++i)
        {
            getline(cin, _input);

            Students.push_back(_input);
            V.push_back({false, false});
        }

        for (int i = 0; i < 2*N-1; ++i)
        {
            int Idx;
            char Alpha;
            cin >> Idx >> Alpha;

            if (!V[Idx-1].first)
            {
                V[Idx-1].first = true;
                continue;
            }
            
            if (!V[Idx-1].second)
                V[Idx-1].second = true;
        }

        for (int i = 0; i < N; ++i)
        {
            if (!V[i].second)
            {
                cout << count << ' ' << Students[i] << '\n';
                break;
            }       
        }
        count++;
    }

    return 0;
}
```
어떻게 보면 쉬운문제이긴 했는데 복병이 있었다.<br/>

바로 한 줄 입력받기..!!<br/>

```
for (int i = 0; i < N; ++i)
{
    string _input;
    cin >> _input;

    Students.push_back(_input);
    V.push_back({false, false});
}
```
초반부엔 이렇게 했는데 아뿔싸 cin은 띄어쓰기 구분으로 입력받는다.<br/>

그래서 이번 문제에서는 getline을 썼어야 했다.<br/>

```
for (int i = 0; i < N; ++i)
{
    string _input;
    getline(cin, _input);

    Students.push_back(_input);
    V.push_back({false, false});
}
```

그래서 입력부를 위와 같이 수정했었다.<br/>

그런데 또 에러가 나서 뭔가 봤더니

```
cin >> N;
cin.ignore();
getline(cin, _input);
```

형태로 가야한다고 하더라..<br/>

그래서 또 아래와 같이 수정했다.<br/>

```
for (int i = 0; i < N; ++i)
{
    string _input;
    cin.ignore();
    getline(cin, _input);

    Students.push_back(_input);
    V.push_back({false, false});
}
```
그래서 테스트 케이스를 출력했더니

```
// 입력
3
Betty Boolean
Alison Addaway
Carrie Carryon
1 B
2 A
3 B
3 A
1 A
2
Helen Clark
Margaret Thatcher
1 B
2 B
2 A
0

// 출력
1 lison Addaway
2 Helen Clark
```

A가 뺴먹고 출력이 되는 것이다..<br/>

첫 번째만 잘 입력이 되고 다음 입력에 대해서 ignore가 되지 않는 문제라 생각해서<br/>

마지막으로 아래와 같이 수정해주었다.<br/>

```
string _input;
for (int i = 0; i < N; ++i)
{
    getline(cin, _input);

    Students.push_back(_input);
    V.push_back({false, false});
}
```
그랬더니 올바르게 작동이 된다!<br/>

그래서 제출한 정답코드가 맨 위의 코드이다.<br/>

동료분께서 말씀하시길, strtok라이브러리를 활용하는 것도 좋다고 한다.<br/>

찾아봐야겠어.<br/>