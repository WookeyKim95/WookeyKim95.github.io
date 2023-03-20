---
layout: post
title:  "[백준] 1737번_Pibonacci C++"
subtitle:   
date: 2023-03-20 11:03:48 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1737번_Pibonacci C++<br/>
<br/>

다이나믹 프로그래밍을 이용해서 풀었던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <map>
#include <math.h>

using namespace std;

#define INF 1000000000000000000

map<double, long long> dict;

long long recur(double n)
{
    auto iter = dict.find(n);
    if (0 <= n && n <= M_PI)
        return 1;
    
    if (iter != dict.end())
        return iter->second;
    
    dict.insert({n, (recur(n-1) + recur(n-M_PI)) % INF});
    return dict.find(n)->second;
}

int main()
{
    long long N;
    cin >> N;

    cout << recur((double)N);

    return 0;
}
```
<br/>

마지막에 INF로 나눈 나머지를 출력하라고 했으므로<br/>

return 시킬때 % INF를 해준다. 하지 않으면<br/>

수가 너무 커져서 long long 범위를 넘어가 음수로 넘어가버리는 일이 생긴다<br/>
<br/>

## 오답제출코드(시간초과)<br/>
<br/>

```
#include <iostream>

using namespace std;

#define INF 1000000000000000000
#define PI 3.1415926535

long long recur(double n)
{
    if (0 <= n && n <= PI)
        return 1;
    
    return recur(n-1) + recur(n-PI);
}

int main()
{
    long long N;
    cin >> N;

    cout << recur((double)N) % INF;

    return 0;
}
```

<br/>

이 문제를 풀면서 살짝 고민했던 부분은 이 부분이다<br/>

recur을 도는 과정에서 과연 n이 음수로 넘어갈 일이 없을까?<br/>

하지만 다행히 0이상 PI 이하인 수는 모두 1로 리턴을 시켜버리기 떄문에<br/>

n이 음수로 넘어갈 일은 없을 거라 생각이 된다.<br/>

그런데 또 다른 난관에 부딪혔다.<br/>

처음엔 보다시피 재귀함수를 써서 풀었는데 N에 1000을 넣고 시뮬레이션을 하니<br/>

시간이 10초가 넘어간다.<br/>

역시 괜히 골드4문제가 아닌 것 같다.<br/>

구글링을 해서 [참고링크](https://sangminlog.tistory.com/entry/boj-1737)를 참고해보니<br/>

재귀함수와 딕셔너리를 사용했다고 한다.<br/>

그렇다면 map을 써서 해결하는게 좋을 것 같았다.<br/>
<br/>

## 오답제출코드(메모리 초과)<br/>
<br/>

```
#include <iostream>
#include <map>

using namespace std;

#define INF 1000000000000000000
#define PI 3.1415926535

map<double, long long> dict;

long long recur(double n)
{
    auto iter = dict.find(n);
    if (0 <= n && n <= PI)
        return 1;
    
    if (iter != dict.end())
        return iter->second;
    
    dict.insert({n, recur(n-1) + recur(n-PI)});
    return dict.find(n)->second % INF;
}

int main()
{
    long long N;
    cin >> N;

    cout << recur((double)N);

    return 0;
}
```

이 코드에서는 처음엔 PI를 내가 정의해서 사용했었다.<br/>

하지만 메모리 초과가 났다.<br/>

double을 너무 많이 사용해서 그런가 라는 생각이 들어 혹시나 해서<br/>

math.h 헤더를 include시키고 원주율 상수를 M_PI로 사용하였다.<br/>

그랬더니 정답처리 되었다.<br/>

M_PI가 더 정교하기 때문에 더 많은 메모리를 사용할 것이라 생각했는데,<br/>

오히려 더 정교하기 때문에 걸러지는 값들이 많았나보다.<br/>

그래서 dict의 사용량도 define을 사용할 때 보다 더 줄어들어서<br/>

메모리 초과가 나지 않았던 모양이다.<br/>