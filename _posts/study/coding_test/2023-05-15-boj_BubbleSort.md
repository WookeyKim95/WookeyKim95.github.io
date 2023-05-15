---
layout: post
title:  "[백준] 1377_버블 소트 C++"
subtitle:   
date: 2023-05-15 09:29:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1377_버블 소트 C++<br/>
<br/>

버블 소트의 이해에 대한 문제, 창의력이 어느 정도 필요했던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    cin >> N;

    vector<pair<int, int>> v;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        v.push_back(make_pair(_input, i));
    }

    sort(v.begin(), v.end());

    int ans = -1;

    for (int i = 0; i < N; ++i)
    {
        if (ans < v[i].second - i)
            ans = v[i].second - i;
    }

    cout << ans + 1;

    return 0;
}
```

<br/>

문제를 읽고 든 생각은 '무슨 의도지?'였다.<br/>

그냥 문제에 나온 코드를 사용하자니 N의 개수가 50만이라<br/>

시간 초과가 날 것은 뻔하였으니.<br/>

음.. 코드를 해석해보고 한 문장으로 나타내면<br/>

정렬이 완료될 때 까지 버블 소트를 몇번 시행했는지 출력하라는 것이었다.<br/>

다시 한번 말하지만, 직접 정렬을 하면 시간초과 나는 것은 확실해서<br/>

버블 소트를 몇번 했는지 알아낼 필요가 있었다.<br/>

자, 그래서 그 방법은 무엇인가?<br/>

어떤 인덱스가 가장 많이 왼쪽으로 이동했으며, 이동한 칸 수를 구하는 것이다.<br/>

버블 정렬이 한 번 이루어질 때 마다, 인덱스가 왼쪽으로 이동하는 횟수는 1번이다.<br/>

```
1 5 4 2
```
위의 수열이 있다고 해보자.

버블 정렬을 한 번 수행하면,

```
1 4 2 5
```

버블 정렬을 두 번 수행하면,

```
1 2 4 5
```
가 된다.<br/>

이렇게 되면 가장 많이 이동한 인덱스는 2이며, 버블 정렬 2번만에 정렬이 완료되는 것이다.<br/>

여기서 주의할 점은 문제에 있는 코드에 따르면 버블 정렬 횟수 + 1이 출력되므로<br/>

ans + 1의 값을 출력하도록 해야 한다.<br/>

따라서 정답 코드는 상술한 바와 같아진다.<br/>