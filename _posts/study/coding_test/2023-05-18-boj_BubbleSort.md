---
layout: post
title:  "[백준] 1517_버블 소트 C++"
subtitle:   
date: 2023-05-18 04:24:24 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# [백준] 1377_버블 소트 C++<br/>
<br/>

버블 소트의 이해에 대한 문제, 창의력이 어느 정도 필요했던 문제<br/>
<br/>

## 정답제출코드<br/>
<br/>

```
#include <iostream>
#include <vector>

using namespace std;

int N;
vector<int> v;
vector<int> tmp;
long long ans = 0;

void merge(int start, int end)
{
    if (start == end)
        return;
    
    int mid = (start + end) / 2;
    int i = start;
    int j = mid + 1;
    int k = start;
    long long count = 0;

    while (i <= mid && j <= end)
    {
        if (v[i] <= v[j])
        {
            tmp[k++] = v[i++];
            ans += (long long)count;
        }

        else
        {
            tmp[k++] = v[j++];
            count++;
        }
    }

	if (i > mid)
    {
		int s = j;
		while (s <= end)
        {
			tmp[k++] = v[s++];
			count++;
		}
	}
	else
    {
		int s = i;
		while (s <= mid)
        {
			tmp[k++] = v[s++];
			ans += (long long)count;
		}
	}

    for (int l = start; l <= end; ++l)
        v[l] = tmp[l];
}

void mergesort(int start, int end)
{
    if (start < end)
    {
        int mid = (start + end) / 2;
        mergesort(start, mid);
        mergesort(mid + 1, end);
        merge(start, end);
    }
}

int main()
{
    ios::sync_with_stdio(0);
    cin.tie(0);
    cout.tie(0);

    cin >> N;

    for (int i = 0; i < N; ++i)
    {
        int _input;
        cin >> _input;

        v.push_back(_input);
        tmp.push_back(_input);
    }

    mergesort(0, N-1);

    cout << ans;

    return 0;
}
```

<br/>

이 문제도 [1377번](https://www.acmicpc.net/problem/1377)과 마찬가지로 버블 소트를 구현하고 일일이 스왑 횟수를 세려고 했다가는<br/>

시간초과가 뜨면서 망한다.<br/>

처음엔 1377번과 같은 풀이방식을 응용하려고 했지만,<br/>

직접 버블소트를 쓰면서 해보니까 뭔가 이상했다.<br/>

수가 맞지 않아서 1377번과 같은 풀이가 아니라는 것을 깨닫게 되었다.<br>

그렇다면 이거는 어떻게 풀어야했을까?<br/>

힌트를 이용하여 알고리즘 분류가 무엇인지를 보았다.<br/>

그 중 주목이 되는 것 두개..<br/>

- 세그먼트 트리
- 분할 정복

엄.. 세그먼트 트리를 배우지 않았는데..<br/>

분할 정복이라고 하는 것 보니까 머지 소트를 응용하는 것 같았다.<br/>

그리고 구글링을 해보니 역시나.. 대부분 머지 소트를 응용해서 푸셨다.<br/>

참고는 [이 분의 블로그 글](https://ghqls0210.tistory.com/152)을 보고 참고를 하였다.<br/>

분석을 해보니<br/>

핵심은 버블소트의 swap의 개수는 merge 소트 과정에서 교차점의 개수라고 한다.<br/>

merge 소트를 하는 과정 중에서 합쳐지는 과정에서<br/>

오른쪽 영역에 있는 숫자가 왼쪽으로 가고, 왼쪽 영역에 있던 숫자가 오른쪽으로 가는 횟수<br/>

그 수가 swap수와 같다고 한다.<br/>

그 부분을 구현한 것이 이 부분인 것 같았다.<br/>

```
while (i <= mid && j <= end)
{
    if (v[i] <= v[j])
    {
        tmp[k++] = v[i++];
        ans += (long long)count;
    }

    else
    {
        tmp[k++] = v[j++];
        count++;
    }
}
```

분할 정복, 머지 소트에 대해서는 대략적으로 이해가 되지만,<br/>

세그먼트 트리 부분에 대해서는 이해가 되지 않는다.<br/>

결국 구글링 해서 풀었지만 나중에 혼자서 풀 수 있도록 공부를 더 해야겠다.<br/>

그러고보니 플레5였잖아 이거?