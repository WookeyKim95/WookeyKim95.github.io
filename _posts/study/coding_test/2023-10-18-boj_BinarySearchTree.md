---
layout: post
title:  "[백준] 5639_이진 검색 트리 C++"
subtitle:   
date: 2023-10-18 07:01:24 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 5639_이진 검색 트리 C++<br/>
<br/>

재귀를 활용해서 이진검색의 방법을 바꿔보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>

using namespace std;

int Tree[10001];

void PostOrder(int S, int E)
{
    if (S >= E)
        return;
    else if (S == E-1)
    {
        cout << Tree[S] << '\n';
        return;
    }

    int idx = S + 1;
    while (idx < E)
    {
        if (Tree[S] < Tree[idx]) {
			break;
		}
		idx++;
    }


    PostOrder(S+1, idx);
    PostOrder(idx, E);
    cout << Tree[S] << '\n';
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    int N;
    int Idx = 0;

    while (cin >> N)
        Tree[Idx++] = N;

    PostOrder(0, Idx);

    return 0;
}
```

전위 순회를 후위 순회를 바꾸는 과정을 검색하다보니 [참고링크](https://ongveloper.tistory.com/295)를 찾았다.<br/>

그러고보니 입력을 받는 부분에서도 어떻게 구현해야 할 지 몰라서 검색을 할 수 밖에 없었던 것 같다.<br/>

이번 문제와 같은 입력이 주어질 때에는 이렇게 하면 되더라.

```
while (cin >> N)
	Tree[Idx] = N;
```

cin >> N이 true가 되는 동안에는 계속 하도록 말이지.<br/>

자! 이제 본론이다. <br/>

어떻게 하면 전위순회를 후위순회로 잘 바꿀 수 있을까?<br/>

규칙을 살펴보면

```
// 전위순회
50
30
24
5
28
45
98
52
60

// 후위순회
5
28
24
45
30
60
52
98
50
```
이전 노드보다 다음 노드 값이 큰 값이 나오기 전까지는 모두 루트노드, 그리고 왼쪽 자식 관계일 것이다.<br/>

하지만 5 - 28 구간처럼 다음 값이 큰 값이 나온다면 서로 형제노드라는 뜻인거지.<br/>

그래서 형제노드를 발견하고 난 다음에<br/>

왼 - 오(형제노드) - 루트(뿌모노드)를 출력하는 순서대로 코드를 짜주면 정답이 나올 수 있다고 한다.<br/>