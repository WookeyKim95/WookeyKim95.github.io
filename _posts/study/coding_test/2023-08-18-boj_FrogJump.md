---
layout: post
title:  "[백준] 17619_개구리 점프 C++"
subtitle:   
date: 2023-08-18 06:34:27 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 17619_개구리 점프 C++<br/>
<br/>

그리디 알고리즘을 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

struct tree
{
    int num;
    int startx;
    int endx;
    int y;
};

bool cmp(tree a, tree b)
{
    return a.startx < b.startx;
}

vector<tree> Trees;
int G[100001];

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);
    
    int N, Q;

    cin >> N >> Q;

    for (int i = 0; i < N; ++i)
    {
        tree t;
        cin >> t.startx >> t.endx >> t.y;
        t.num = i + 1;
        Trees.push_back(t);
    }

    sort(Trees.begin(), Trees.end(), cmp);

    int sid = 0;
    int e = Trees[0].endx;
    G[Trees[0].num] = sid;

    for (int i = 1; i < N; ++i)
    {
        if (Trees[i].startx <= e)
        {
            if (Trees[i].endx > e)
                e = Trees[i].endx;
        }
        else
        {
            sid++;
            e = Trees[i].endx;
        }
        G[Trees[i].num] = sid;
    }

    for (int i = 0; i < Q; ++i)
    {
        int start, end;
        cin >> start >> end;

        if (G[start] == G[end])
            cout << 1 << '\n';
        else
            cout << 0 << '\n';
    }

    return 0;
}
```

y좌표를 신경쓰다 보니까 문제를 잘 못풀었던 것 같다.<br/>

그런데 [이분의 글](https://leesh111112.tistory.com/189)을 보고 생각해보니 y좌표를 신경쓸 필요가 없었다.<br/>

어차피 x좌표가 겹치면 건너 건너서 가면 되는 것이었잖아?<br/>

그저 x좌표가 겹치는 나무들끼리 같은 그룹으로 묶어서 두 숫자를 입력하면<br/>

같은 그룹에 있는지를 조사해주면 되는 것이었다.<br/>

그 절차는 아래와 같다.<br/>

1. 입력받은 통나무들을 왼쪽 x지점을 기준으로 정렬을 해준다.
2. 0번 sid부터 시작해서 그룹을 묶기 시작한다.
3. 만약에 이번 통나무의 endx 지점이 다음 통나무의 startx지점보다 오른쪽에 있다면 같은 그룹에 묶는다.
4. 그렇지 않다면 sid를 1 증가시켜서 다른 그룹에 묶기 시작하고 e 포인트를 갱신해준다.

그 다음에 start와 end가 같은 그룹 안에 있는지 조사를 해주면 된다.<br/>

그룹 번호를 배열로 저장하면 간단하다.<br/>