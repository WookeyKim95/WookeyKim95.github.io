---
layout: post
title:  "[백준] 9375번_패션왕 신해빈 C++"
subtitle:   
date: 2023-02-23 10:26:43 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 9375번_패션왕 신해빈 C++<br/>
<br/>

해시와 조합론을 활용하는 문제<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <map>
#include <string>
#include <vector>

using namespace std;

int main()
{
    int testCase;
    cin >> testCase;

    for (int i = 0; i < testCase; ++i)
    {
        int N;
        cin >> N;

        map<string, vector<string>> fashion;
        
        string cloth, part;
        for (int j = 0; j < N; ++j)
        {
            cin >> cloth >> part;

            auto iter = fashion.find(part);

            // 해당파트가 없을 경우 새로 삽입
            if (iter == fashion.end())
            {
                vector<string> clothes;
                clothes.push_back(cloth);
                fashion.insert(make_pair(part, clothes));
            }
            // 해당 파트가 있을 경우 해당 파트에 옷 삽입
            else
            {
                iter->second.push_back(cloth);
            }
        }

        int count = 1;
        
        // 옷 가지수 + 안입는 경우를 곱함.
        for (auto iter = fashion.begin(); iter != fashion.end(); ++iter)
            count *= (iter->second.size() + 1);

        // 알몸인 경우 제외
        cout << count - 1 << '\n';
    }
    return 0;
}
```
<br/>

해시를 만드는 과정까지는 괜찮았는데 경우의 수를 생각하는 과정에서 오래 걸렸다.<br/>

잘 생각해보니, 그냥 종류별 옷 가지수 + 안입는 경우(1가지) 를 카운팅에 곱하고,<br/>

알몸인 경우를 제외시키면 되는 것이었다.<br/>