---
layout: post
title:  "[프로그래머스] 피로도 C++"
subtitle:   
date: 2023-01-14 10:02:23 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 피로도 C++<br/>
<br/>

완전 탐색 문제
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>
#include <vector>

using namespace std;

bool visited[5000] = {false};
int counter_save = 0;

void backtrack(int k, vector<vector<int>> dungeons, int count)
{
    if (counter_save < count)
        counter_save = count;

    if (dungeons.size() == count)
        return;
    
    for (int i = 0; i < dungeons.size(); ++i)
    {
        if (!visited[i] && k >= dungeons[i][0])
        {
            visited[i] = true;
            backtrack(k - dungeons[i][1], dungeons, count+1);
            visited[i] = false;
        }
    }
}

int solution(int k, vector<vector<int>> dungeons) {
    int answer = -1;
    backtrack(k, dungeons, 0);
    answer = counter_save;
    return answer;
}
```

백트래킹을 사용하여 문제를 풀었다.<br/>

다만 dungeons 벡터에 들어있는 인자를 전역 변수로 저장하기 보다는<br/>

함수의 인자를 재귀함수에 다시 넣는 방법으로 사용해서 조금 복잡해보일수 있을 것 같다.<br/>