---
layout: post
title:  "[백준] 1241번_머리 톡톡 C++"
subtitle:   
date: 2023-03-22 09:16:43 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1241번_머리 톡톡 C++<br/>
<br/>

시간복잡도 측면에서 중요했던 수학 문제.<br/>
<br/>

## 정답제출코드 <br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;
vector<int> friends;
int results[100001] = {0};
int numbers[1000001] = {0};

int main()
{
    ios_base::sync_with_stdio(false);
	cin.tie(NULL);
    cout.tie(NULL);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        friends.push_back(_input);
        numbers[_input] += 1;
    }

    for (int i = 0; i < N; ++i)
    {
        int k = 1;
        while (k * k <= friends[i])
        {
            if (friends[i] % k == 0)
            {
                if (k * k != friends[i])
                    results[i] += numbers[k] + numbers[friends[i]/k];

                else
                    results[i] += numbers[k];
            }
            k++;
        }
        cout << results[i]-1 << '\n';
    }

    return 0;
}
```

일반적으로 숫자 하나마다 배수를 따지면 시간이 오래걸린다.<br/>

그래서 [참고링크](https://blog.naver.com/PostView.nhn?isHttpsRedirect=true&blogId=jaeyoon_95&logNo=222363935037&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)에 있는 내용을 참고하여 코드를 작성하였다.<br/>

원리는 약수의 개수이다.<br/>

numbers에 약수의 개수를 저장하고 k가 frineds[i]에 있는 수의 약수이면<br/>

그 갯수를 더하는 식으로 구현하였다.<br/>

<br/>

## 오답제출코드(시간 초과)<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;
vector<int> friends;

bool IsMultiple(int a, int b)
{
    if (a % b == 0)
        return true;
    else
        return false;
}

int main()
{
    ios_base::sync_with_stdio(false);
	cin.tie(NULL);
    cout.tie(NULL);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        friends.push_back(_input);
    }

    for (int i = 0; i < N; ++i)
    {
        int count = 0;
        for (int j = 0; j < N; ++j)
        {
            if (i == j)
                continue;
            if (IsMultiple(friends[i], friends[j]))
                count++;
        }
        cout << count << '\n';
    }

    return 0;
}
```

골드5문제 치고는 쉽게 풀리는 것 같다 했는데<br/>

시간 초과가 났다. 역시나 쉽게 풀리면 골드5가 아니지.<br/>

탐색하고 배수 관계인지를 판별하는 부분에서 오래 걸린 것 같다.<br/>

이 코드는 시간복잡도가 O(n^2)이지만 더 줄일 필요가 있던 것 같다.<br/>
<br/>

