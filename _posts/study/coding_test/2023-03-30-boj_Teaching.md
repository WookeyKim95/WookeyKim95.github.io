---
layout: post
title:  "[백준] 1062번_가르침 C++"
subtitle:   
date: 2023-03-30 10:37:04 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1062번_가르침 C++<br/>
<br/>

백트래킹을 이용해서 푸는 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <string>
#include <vector>

using namespace std;

int N, M;
vector<string> words;
int ans = 0;
bool visited[26] = {false};
vector<char> selected = {'a', 'c', 'i', 'n', 't'};

bool isIn(char b)
{
    for (size_t i = 0; i < selected.size(); ++i)
    {
        if (selected[i] == b)
            return true;
    }
    return false;
}

int check()
{
    int result = 0;
    for (int i = 0; i < N; ++i)
    {
        bool flag = true;
        for (char j : words[i])
        {
            if (!isIn(j))
            {
                flag = false;
                break;
            }
        }
        if (flag)
            result++;
    }
    return result;
}

void backtrack(int idx, int depth)
{
    if (depth == M - 5)
    {
        int result = check();
        if (result > ans)
            ans = result;
        return;
    }
    
    for (int i = idx; i < 26; ++i)
    {
        if (!visited[i])
        {
            visited[i] = true;
            selected.push_back(char(i) + 'a');
            backtrack(i, depth+1);
            selected.pop_back();
            visited[i] = false;
        }
    }
}

int main()
{
    cin >> N >> M;

    for (int i = 0; i < N; ++i)
    {
        string _input;
        cin >> _input;

        words.push_back(_input);
    }

    if (M < 5)
    {
        cout << 0;
        return 0;
    }

    else if (M == 26)
    {
        cout << N;
        return 0;
    }


    vector<char> MustLearn = {'a', 'c', 'i', 'n', 't'};
    for (int i = 0; i < 5; ++i)
    {
        visited[MustLearn[i] - 'a'] = true;
    }


    backtrack(0, 0);

    cout << ans;

    return 0;
}
```

[참고링크](https://kyun2da.github.io/2020/09/26/teaching/)를 참고해서 풀었다.<br/>
<br/>

우선 a, c, i, n, t는 필수단어 이므로 방문여부에 처음부터 체크를 해준다.<br/>

```
vector<char> MustLearn = {'a', 'c', 'i', 'n', 't'};
for (int i = 0; i < 5; ++i)
{
    visited[MustLearn[i] - 'a'] = true;
}
```
<br/>

그리고 백트래킹을 통해서 가르친 글자 목록을 만든 다음에<br/>

```
void backtrack(int idx, int depth)
{
    if (depth == M - 5)
    {
        int result = check();
        if (result > ans)
            ans = result;
        return;
    }
    
    for (int i = idx; i < 26; ++i)
    {
        if (!visited[i])
        {
            visited[i] = true;
            selected.push_back(char(i) + 'a');
            backtrack(i, depth+1);
            selected.pop_back();
            visited[i] = false;
        }
    }
}
```
<br/>

입력된 단어에 있는 알파벳 중에 하나라도 가르친 글자 목록에 없다면<br/>

그 단어는 체크를 하지 않는 것으로 구현하였다.<br/>

```
bool isIn(char b)
{
    for (size_t i = 0; i < selected.size(); ++i)
    {
        if (selected[i] == b)
            return true;
    }
    return false;
}

int check()
{
    int result = 0;
    for (int i = 0; i < N; ++i)
    {
        bool flag = true;
        for (char j : words[i])
        {
            if (!isIn(j))
            {
                flag = false;
                break;
            }
        }
        if (flag)
            result++;
    }
    return result;
}
```

check를 마치고 난 다음에는 기존에 있던 정답과 도출된 결과 값중에 큰 값을 정답에 넣어주었다.<br/>

그렇게 해서 정답 코드를 상술한 바와 같이 구성하였다.<br/>