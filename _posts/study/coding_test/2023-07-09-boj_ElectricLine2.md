---
layout: post
title:  "[백준] 2568_전깃줄 2 C++"
subtitle:   
date: 2023-07-09 08:15:21 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 2568_전깃줄 2 C++<br/>
<br/>

LIS를 이용해보는 문제<br/>
<br/>

## 정답제출코드<br/>
<Br/>

```
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

vector<pair<int, int>> line;
int N;
vector<int> idx, ans, LIS;

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int s, e;
        cin >> s >> e;

        line.push_back({s, e});
    }

    sort(line.begin(), line.end());

    LIS.push_back(line[0].second);
    idx.push_back(0);

    for (int i = 1; i < N; ++i)
    {
        int Cur = line[i].second;

        if (LIS.back() < Cur)
        {
            idx.push_back(LIS.size());
            LIS.push_back(Cur);
        }

        else
        {
            int left = 0;
            int right = LIS.size() - 1;

            while (left < right)
            {
                int mid = (left + right) / 2;
                
                if (LIS[mid] < Cur)
                    left = mid + 1;
                
                else
                    right = mid;
            }

            LIS[left] = Cur;
            idx.push_back(left);
        }
    }

    cout << N - LIS.size() << '\n';

    int Cur = LIS.size() - 1;

    for (int i = idx.size()-1; i >= 0; --i)
    {
        if (Cur == idx[i])
            Cur--;
        else
            ans.push_back(line[i].first);
    }

    sort(ans.begin(), ans.end());
    for (size_t i = 0; i < ans.size(); ++i)
        cout << ans[i] << '\n';

    return 0;
}
```

[참고링크](https://uyt8989.tistory.com/98)를 참고해서 풀었다.<br/>

<br/>

우선 LIS를 사용해야했기에 투 포인터가 필요할 것이라고 생각했다.<br/>

그런데 위에 올린 참고링크를 보니 lower_bound라는 함수를 통해서<br/>

투 포인터를 쓰지 않고도 구현이 가능하다는 것을 알게 되었다.<br/>

즉,

```
int left = 0;
int right = LIS.size() - 1;

while (left < right)
{
    int mid = (left + right) / 2;
    
    if (LIS[mid] < Cur)
        left = mid + 1;
    
    else
        right = mid;
}
```
이 코드가

```
vector<int>::iterator iter = lower_bound(LIS.begin(), LIS.end(), cur);
```

이 코드랑 같은 효과를 가지는 것이다.<br/>

구현은 투 포인터를 이용해서 구현되어 있을 것 같다.<br/>

투 포인터만 생각하면 되는 줄 알았는데,<br/>

정답을 출력하는 과정이 생각보다 까다로웠다.<br/>

```
int Cur = LIS.size() - 1;

for (int i = idx.size()-1; i >= 0; --i)
{
    if (Cur == idx[i])
        Cur--;
    else
        ans.push_back(line[i].first);
}

sort(ans.begin(), ans.end());
for (size_t i = 0; i < ans.size(); ++i)
    cout << ans[i] << '\n';
```

idx와 LIS의 인덱스 값을 비교하면서 서로 같다면 잘리지 않은 선,<br/>

그렇지 않다면 잘린 선으로 취급하여 ans에 넣어준다.<br/>

그리고 ans를 정렬한 뒤에 출력하면 끝.