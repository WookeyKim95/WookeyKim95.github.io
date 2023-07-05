---
layout: post
title:  "[백준] 27172_수 나누기 게임 C++"
subtitle:   
date: 2023-07-06 07:33:05 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 27172_수 나누기 게임 C++<br/>
<br/>

에라토스테네스의 체를 활용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <cstring>

#define MAX 1000001

using namespace std;

int player[MAX];
vector<int> card;
bool HasCard[MAX];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N, Max = 0;
    cin >> N;

    memset(player, 0, sizeof(player));
    memset(HasCard, false, sizeof(HasCard));

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        if (Max < _input)
            Max = _input;

        card.push_back(_input);
        HasCard[_input] = true;
    }

    
    for (size_t i = 0; i < card.size(); ++i)
    {
        int Index = card[i];
        for (int j = Index*2; j <= Max; j += Index)
        {
            if (HasCard[j])
            {
                player[Index]++;
                player[j]--;
            }
        }
    }

    for (size_t i = 0; i < card.size(); ++i)
        cout << player[card[i]] << ' ';

    return 0;
}
```

처음 보았을 때 생각은 굳이 에라토스테네스의 체를 사용해야하나?<br/>

라는 생각이 들었지만, 제한시간이 1초인 것을 보고 그래야겠다는 생각이 들었다.<br/>

N의 최대값이 100000이면 나머지 연산자를 일일이 사용한다면 O(N^2)시간 복잡도가 나오면<br/>

이를 초과해버리겠지.<br/>

그래서 O(N)이 나오도록 에라토스테네스의 체를 사용해야겠다는 생각이 들었다.<br/>

에라토스테네스의 체의 모양을 이용해서 해당 값이 적힌 카드가 있는지 확인한 뒤<br/>

확인이 된다면 그를 가지고 있는 플레이어와 결투를 하여 점수를 변경한다.<br/>

```
for (size_t i = 0; i < card.size(); ++i)
{
    int Index = card[i];
    for (int j = Index*2; j <= Max; j += Index)
    {
        if (HasCard[j])
        {
            player[Index]++;
            player[j]--;
        }
    }
}
```

그리고 마지막에 점수를 출력한다.<br/>

여기서 처음에는 점수를 보관하는 용도로 아래와 같이 벡터를 만들었었다.<br/>

```
vector<int> player // 플레이어의 점수를 나타내는 벡터

...

for (int i = 0; i < N; ++i)
    player.push_back(0);
```

하지만, 이렇게 하기보다 플레이어가 가지고 있는 카드 값에다가 점수를 놓는 것이<br/>

더 효율적이라고 [참고링크](https://uigwonblog.tistory.com/36)를 통해서 알 수 있었다.<br/>

에라토스테네스의 체를 사용하는 과정 자체는 어렵지 않았는데,<br/>

점수를 어떻게 변경시킬지 나타내는 과정이 어려웠던 것 같다.<br/>