---
layout: post
title:  "[알고리즘] 퀵 정렬"
subtitle:
date: 2022-02-10 09:31:22 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

## 퀵 정렬이란?<br/>

리스트에서 일정한 기준 값을 정해서 기준 값보다 작은 수는 왼쪽으로 보내고, 기준 값보다 큰 수는 오른쪽으로 보내서 새로운 리스트를 형성한 다음에 재귀호출로 새로운 리스트들에 대해 위 과정을 반복하는 과정이다.<br/>

![퀵정렬과정](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-10_quick_1.jpg?raw=true)

이렇게 재귀호출을 반복하다가, 입력받은 리스트의 길이가 1이면 그 값을 그대로 반환하면서 길이가 1인 리스트들을 다시 합치면 정렬이 완료된다.<br/>
<br/>

### 파이썬 코드 구현<br/>

```
def quick_sort(list_input):
    if len(list_input) <= 1:
        return list_input
    
    pivot = list_input[0]
    left_list = []
    right_list = []
    
    for i in list_input[1:]:
        if i >= pivot:
            right_list.append(i)
        else:
            left_list.append(i)
            
    return quick_sort(left_list) + [pivot] + quick_sort(right_list)
```

위와 같이 구현이 가능하다.<br/>
단, 주의할 점은 return 과정에서 리스트 + 리스트 + 리스트를 연산하는 것이기 때문에 pivot이 int로 선언되었던 것을 기억하자.<br/>

`return quick_sort(left_list) + pivot + quick_sort(right_list)`<br/>

위와 같이 썼다가 오류가 일어날 수 있다.<br/>
pivot 선정기준은 그냥 편하게 리스트 맨 앞에 있는 수를 설정했지만, 어떤 수를 pivot으로 설정할지는 코드를 작성하는 사람의 마음이다.<br/>

### list Comprehension으로 코드 축약하기

```
    pivot = list_input[0]
    left_list = []
    right_list = []
    
    for i in list_input[1:]:
        if i >= pivot:
            right_list.append(i)
        else:
            left_list.append(i)
```

이 부분을

```
    pivot = list_input[0]
    left_list = [ i for i in list_input[1:] if i <= pivot ]
    right_list = [ i for i in list_input[1:] if i > pivot ]
```
아래와 같이 축약할 수 있다.<br/>
<br/>
<br/>

참고 링크 : [잔재미코딩_퀵정렬](https://fun-coding.org/Chapter15-quicksort.html)
