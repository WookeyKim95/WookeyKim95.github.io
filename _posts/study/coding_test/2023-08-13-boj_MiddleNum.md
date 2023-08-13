---
layout: post
title:  "[백준] 1572_중앙값 C++"
subtitle:   
date: 2023-08-13 07:12:25 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1572_중앙값 C++<br/>
<br/>

세그먼트 트리를 응용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>
#include <cmath>
#include <algorithm>

using namespace std;

#define MAXR 1000001
#define MAX 65535

int N, K;
long long ans = 0;
long long arr[MAXR];
long long SegTree[MAXR];

void init(int Node, int Start, int End, long long Value, long long Diff)
{
    if (Start == End)
    {
        SegTree[Node] += Diff;
        return;
    }
        
    long long Mid = (Start + End) / 2;

    if (Value <= Mid)
        init(Node*2, Start, Mid, Value, Diff);
    else
        init(Node*2+1, Mid+1, End, Value, Diff);
    
    SegTree[Node] = SegTree[Node*2] + SegTree[Node*2+1];
}

long long GetValue(int Node, long long Start, long long End, long long Value)
{
    if (Start == End)
        return Start;
    
    int Mid = (Start + End) / 2;

    if (Value <= SegTree[Node*2])
        return GetValue(Node*2, Start, Mid, Value);
    else
        return GetValue(Node*2+1, Mid+1, End, Value - SegTree[Node*2]);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> K;


    for (int i = 0; i < N; ++i)
        cin >> arr[i];

    for (int i = 0; i < K-1; ++i)
        init(1, 0, MAX, arr[i], 1);

    int Idx = 0;

    for (int i = K-1; i < N; ++i)
    {
        init(1, 0, MAX, arr[i], 1);
        ans += GetValue(1, 0, MAX, (K+1) / 2);
        init(1, 0, MAX, arr[Idx++], -1);
    }

    cout << ans;

    return 0;
}
```

구간, 합 이라는 키워드가 들어갔고 범위도 많이 큰 것을 보니<br/>

세그먼트 트리를 써야겠다는 생각을 하긴 했다.<br/>

하지만 이번 문제는 그렇게 만만하진 않았다. (플레5인데 당연하지..)<br/>

세그먼트르 트리를 어떻게 응용하고 다른 알고리즘과 어떻게 결합할지 생각해야했다.<br/>

처음에 테스트케이스 1, 2번을 보고 이렇게 생각했다.<br/>

그냥 배열 입력받고, 정렬하고 어디서 어디까지 합치면 되는지 생각하면 되는 것 아닌가?<br/>

하지만 아니었다.<br/>

생각보다 많이 까다로웠고, 테스트 케이스 3를 만족시킬 수 있는 방법이 떠오르지 않아서<br/>

구글링을 해보기로 했다.<br/>

이번에는 [이 분의 글](https://littlesam95.tistory.com/entry/BOJPlatinum-5-%EB%B0%B1%EC%A4%80-1572-%EC%A4%91%EC%95%99%EA%B0%92C)을 참고하였다.<br/>

이 분의 글에 따르면<br/>

1. 0번째 수부터 K-1번째 수를 세그먼트 트리에 업데이트하기
2. K번째 수를 업데이트 할 때 중앙 값을 찾는 데 현재 구간의 중앙값보다 작으면
왼쪽 자식을, 크면 오른쪽 자식을 탐색
3. 반복.

결국 이진탐색 기법까지 써야하는 문제였던 것이다.<br/>

솔직히 완벽하게 이해는 안됐고, 플레 5급을 풀 실력은 아닌것 같다.<br/>

선수문제들을 조금 더 풀어보고 다시 풀어봐야 할 것 같다.<br/>