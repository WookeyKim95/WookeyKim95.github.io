---
layout: post
title:  "[백준] 2096_내려가기 C++"
subtitle:   
date: 2023-06-26 08:05:56 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2096_내려가기 C++<br/>
<br/>

슬라이딩 윈도우에 배워보는 DP 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int MaxArr[2][3];
int MinArr[2][3];
int N, AnsMax, AnsMin;

int m_max(int a, int b)
{
    if (a > b)
        return a;
    else
        return b;
}

int m_min(int a, int b)
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

    cin >> N;

    int x, y, z, k = 0;
    for (int i = 0; i < N; ++i)
    {
        cin >> x >> y >> z;

        MaxArr[k][0] = m_max(MaxArr[1-k][0], MaxArr[1-k][1]) + x;
		MaxArr[k][1] = m_max(m_max(MaxArr[1-k][0], MaxArr[1-k][1]), MaxArr[1-k][2]) + y;
		MaxArr[k][2] = m_max(MaxArr[1-k][1], MaxArr[1-k][2]) + z;
		
		MinArr[k][0] = min(MinArr[1-k][0], MinArr[1-k][1]) + x;
		MinArr[k][1] = min(min(MinArr[1-k][0], MinArr[1-k][1]), MinArr[1-k][2]) + y;
		MinArr[k][2] = min(MinArr[1-k][1], MinArr[1-k][2]) + z;
		
		k = 1 - k;
    }

    AnsMax = m_max(m_max(MaxArr[1-k][0], MaxArr[1-k][1]), MaxArr[1-k][2]);
    AnsMin = min(min(MinArr[1-k][0], MinArr[1-k][1]), MinArr[1-k][2]);

    cout << AnsMax << ' ' << AnsMin;

    return 0;
}
```
<br/>

문제를 보고나서 오 DP구나! 생각을 하고 DP 배열을 아래와 같이 선언하려고 했다.<br/>

```
int MaxArr[3][100001];
int MinArr[3][100001];
```

그런데 문제 조건을 보니 제한 메모리가 4MB였다.<br/>

처음 보는 나로서는 이 무슨 경악스러운 수치인가..?<br/>

만약 내가 처음 생각했던 대로 배열을 선언하면 아마 10MB는 훌쩍 넘지 않을까 생각했다.<br/>

그래서 무슨 알고리즘을 사용하는지 궁금해서 알고리즘 분류를 열어보았는데<br/>

슬라이딩 윈도우?? 이게 뭐지??<br/>
<br/>

### 슬라이딩 윈도우<br/>
<Br/>

[참고링크](https://learning-e.tistory.com/36)에서 슬라이딩 윈도우에 대해 알아보았다.<br/>

내가 해석한대로 적어보자면<br/>

배열이 있으면 배열 내에서 어떤 범위가 주어졌을 때 범위의 내부 요소 값을 이용해 문제를 푸는 알고리즘이다.<br/>

```
1, 2, 3, 4, 5, 4, 5, 2, 8
```

이라는 배열이 있고 윈도우의 범위가 5라고 할 때

```
{1, 2, 3, 4, 5}, 4, 5, 2, 8
1, {2, 3, 4, 5, 4}, 5, 2, 8
1, 2, {3, 4, 5, 4, 5}, 2, 8
1, 2, 3, {4, 5, 4, 5, 2}, 8
1, 2, 3, 4, {5, 4, 5, 2, 8}
```

이렇게 순차적으로 계산할 때 앞에 있는 값을 빼고 뒤에 있는 값을 넣는 것이 마치<br/>

윈도우가 슬라이딩 하는 것 처럼 보여 슬라이딩 윈도우라고 불러지게 되는 것이다.<br/>

<br/>

### 그럼 이 문제에서 적용하는 방법은?<br/>
<Br/>

범위가 2인 슬라이드 윈도우를 만드는 것이다.<br/>

만약에 4줄이 있다고 치면 아래와 같이 연산이 진행될 것이다.<br/>

```
(1 2 3)
(4 5 6)
7 8 9
4 9 0
```

```
1 2 3
(4 5 6)
(7 8 9)
4 9 0
```

```
1 2 3
4 5 6
(7 8 9)
(4 9 0)
```

즉, 처음부터 수를 한꺼번에 입력받지 말고, 한 줄씩 내려가면서 수를 입력받는다.<br/>

그리고 k값이 0과 1을 왔다갔다 하게 만들기 위해서 처음에는 k를 0으로 선언해놓고, k = 1-k로 이동시킨다.<br/>

연산코드를 나타내면 아래와 같다.<br/>

```
int x, y, z, k = 0;
for (int i = 0; i < N; ++i)
{
    cin >> x >> y >> z;

    MaxArr[k][0] = m_max(MaxArr[1-k][0], MaxArr[1-k][1]) + x;
    MaxArr[k][1] = m_max(m_max(MaxArr[1-k][0], MaxArr[1-k][1]), MaxArr[1-k][2]) + y;
    MaxArr[k][2] = m_max(MaxArr[1-k][1], MaxArr[1-k][2]) + z;
    
    MinArr[k][0] = min(MinArr[1-k][0], MinArr[1-k][1]) + x;
    MinArr[k][1] = min(min(MinArr[1-k][0], MinArr[1-k][1]), MinArr[1-k][2]) + y;
    MinArr[k][2] = min(MinArr[1-k][1], MinArr[1-k][2]) + z;
    
    k = 1 - k;
}
```

이렇게 범위가 2줄인 슬라이딩 윈도우를 이동시켜서 최소, 최대값을 구하면 된다.<br/>