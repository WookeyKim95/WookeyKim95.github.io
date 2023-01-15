---
layout: post
title:  "[프로그래머스] 방문 길이 C++"
subtitle:   
date: 2023-01-15 14:04:27 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [프로그래머스] 방문 길이 C++<br/>
<br/>

프로그래머스 썸머코딩/윈터코딩 기출 문제
<br/>

## 정답제출코드<br/>
<br/>

```
#include <string>

using namespace std;

bool visited[11][11][11][11] = {false};

int solution(string dirs) {
    int answer = 0;
    
    int len = dirs.length();
    
    // x, y = (-5, -5) 지점을 point의 [0][0] 지점으로 잡음.
    // 따라서. x, y = (0, 0) 지점은 point의 [5][5]가 됨.
    int point[2] = {5, 5};
    
    for (int i = 0; i < len; ++i)
    {   
        // 이동하기 전의 좌표 임시 저장
        int tmp[2] = {point[0], point[1]};
        if (dirs[i] == 'L')
        {
            if (point[0] > 0)
                point[0] -= 1;
        }
        
        else if (dirs[i] == 'R')
        {
            if (point[0] < 10)
                point[0] += 1;
        }
        
        else if (dirs[i] == 'U')
        {
            if (point[1] < 10)
                point[1] += 1;
        }
        
        else if (dirs[i] == 'D')
        {
            if (point[1] > 0)
                point[1] -= 1;
        }
        
        // 방문하지 않았을 경우 visited에 대해서 true로 바꿔줌.
        // 그리고 무시된 명령이 아닌 경우에도 바꿔줌.
        if (!visited[point[0]][point[1]][tmp[0]][tmp[1]]
            && !(point[0] == tmp[0] && point[1] == tmp[1]))
        {
            answer++;
            // 해당 길 방문 여부 기록
            // (x1, y1)과 (x2, y2) 사이의 길을 방문했다고 기록해야함.
            visited[point[0]][point[1]][tmp[0]][tmp[1]] = true;
            visited[tmp[0]][tmp[1]][point[0]][point[1]] = true;
        }
    }
    
    return answer;
}
```

4차원 짜리 리스트에다가 visited 여부를 기록하는 방식으로 풀었다.<br/>

- visited[이동 후 x 좌표][이동 후 y 좌표][이동 전 x 좌표][이동 전 y 좌표]에 방문여부를 기록하는 형식으로 자료구조를 형성했다.

- 허용 범위 내에 있을 경우 point[0], point[1]을 변화시킨다.

- 이동 전 좌표를 저장하는 tmp를 활용한다. tmp와 point의 값이 같을 경우 이동하지 않았다, 즉 명령이 무시되었다는 의미이다.

- 만약에 visited[x2][y2][x1][y1]이 false면 true로 바꾸면서 answer를 1 더해준다.

- 그리고 정확히는 (x2, y2)와 (x1, y1) 사이의 길이다. 따라서 행렬상 대칭 점에 있는 visited[x1][y1][x2][y2]도 true로 바꿔줘야 한다.