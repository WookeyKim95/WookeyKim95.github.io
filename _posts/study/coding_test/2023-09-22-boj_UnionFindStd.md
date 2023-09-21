---
layout: post
title:  "[백준] 1717_집합의 표현 C++"
subtitle:   
date: 2023-09-22 07:07:22 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1717_집합의 표현 C++<br/>
<br/>

유니온 파인드의 기초를 배워보는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N, M;
vector<int> Parent;

int Find(int a)
{
    if (a == Parent[a])
        return a;
    
    return Parent[a] = Find(Parent[a]);
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N >> M;

    for (int i = 0; i <= N; ++i)
        Parent.push_back(i);

    for (int i = 0; i < M; ++i)
    {
        int _input, a, b;
        cin >> _input >> a >> b;

        if (_input == 1)
        {
            if (Find(a) == Find(b))
                cout << "YES\n";
            else
                cout << "NO\n";
        }
        else if (_input == 0)
        {
            int aP = Find(a);
            int bP = Find(b);

            Parent[aP] = Parent[bP];
        }
    }

    return 0;
}
```

이 코드가 곧 유니온 파인드의 기본 틀이라고 부를 수 있다.<br/>

유니온 파인드라는 알고리즘은<br/>

어떤 노드의 부모가 같은지 여부를 판별하면서 부모를 같게 만들어줌으로써<br/>

합집합을 만들어가거나 같은 집합인지 여부를 판별하는 과정이다.<br/>

```
for (int i = 0; i <= N; ++i)
    Parent.push_back(i);
```
우선, 자기 자신을 부모로 설정하면서 초기화를 한다.

```
int Find(int a)
{
    if (a == Parent[a])
        return a;
    
    return Parent[a] = Find(Parent[a]);
}
```

그리고 이 코드는 부모를 찾아가면서 연결된 노드끼리 부모를 모두 같게 만들어주는 코드이다.<br/>

```
else if (_input == 0)
{
    int aP = Find(a);
    int bP = Find(b);

    Parent[aP] = bP;
}
```
그리고 이 코드는 조금 더 복잡한 유니온 파인드 알고리즘 문제를 풀 때가 되면<br/>

Union이라는 함수로 빼게 될 것이다.<br/>

두 집합의 최상위에 있는 부모를 찾아서 최상위의 부모를 같게 만들어주는 과정이다.<br/>

이렇게 하면 집합이 합쳐진다.<br/>

```
if (_input == 1)
{
    if (Find(a) == Find(b))
        cout << "YES\n";
    else
        cout << "NO\n";
}
```

그리고 부모가 같은지 여부를 판별할 수 있는 코드이다.<br/>

여기서 실수를 할 수 있는 점이,<br/>

```
if (_input == 1)
{
    if (Parent[a] == Parent[b])
        cout << "YES\n";
    else
        cout << "NO\n";
}
```
처음엔 위와 같은 코드로 적었다.<br/>

하지만 이렇게하면 NO가 출력될 수 있는 상황이 있다.<br/>

부모를 합치는 코드는 Parent[aP] = bP로,<br/>

여기서는 단순히 Parent[aP] = bP로 만들어주는 것이다.<br/>

즉, 각 노드의 최상위 부모 목록이 {0, 1, 0, 1, 0, 1} 라고 해보자. (인덱스는 0부터)<br/>

이때, a = 2, b = 3이라고 하면<br/>

최상위 부모, Parent[a] = aP, Parent[b] = bP라고 했을 때,<br/>

현재 aP = 0, bP = 1이다.<br/>

여기서 Parent[aP] = bP를 하면<br/>

{1, 1, 0, 1, 0, 1}이 되는 것이다.<br/>

여기서 이전에 aP집합에 속해 있었던 Parent[a] == Parent[b]를 조사한다면?<br/>
(a = 2, b = 3)<br/>

false가 나오겠지. 왜냐하면 아직 최상위 부모만 바꿨을 뿐 하위에서는 최상위 부모노드가 안바뀌었으니까.<br/>

즉, 나는 aP집합이랑 bP집합을 합쳤기 떄문에 바로 최상위 부모도 조사해주면 될줄 알았지만<br/>

하위노드의 부모까지 바꾸는 과정은 바로 Find에서 일어나는 것이기 때문에<br/>

조사를 할 때에도<br/>

```
if (_input == 1)
{
    if (Find(a) == Find(b))
        cout << "YES\n";
    else
        cout << "NO\n";
}
```

이렇게 Find를 써서 조사를 해주어야 오류가 일어나지 않는다.<br/>