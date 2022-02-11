---
layout: post
title:  "[알고리즘] 병합 정렬"
subtitle:
date: 2022-02-11 19:50:53 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

## 병합 정렬이란?<br/>

리스트를 길이가 1이 될 때 까지 반으로 나눈 후, (길이가 홀수인 n일 경우에는 n-1과 n 혹은 n과 n-1) 다시 쪼개진 리스트 내에서 크기를 비교하면서 합치면서 정렬하는 과정이다.<br/>

![병합정렬과정](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-11_merge_1.jpg?raw=true)

퀵 정렬과 과정이 비슷한데, 차이점이라면 퀵정렬은 기준점을 정해서 작은 쪽을 왼쪽으로 보내고 큰 쪽은 오른쪽으로 보내고 리스트의 길이가 1이 되면 그대로 합치는 반면, 병합 정렬은 우선 쪼개고 나서 합칠 때 크기 비교를 한다.<br/>

## 파이썬 코드 구현<br/>

파이썬 코드로 구현한다면 아래와 같이 구현이 가능하다.<br/>

```
def merge(left, right):
    i, j = 0, 0
    list_return = []
    
    while 1:
        if i == len(left):
            list_return += right[j:]
            break
        elif j == len(right):
            list_return += left[i:]
            break
        
        if left[i] < right[j]:
            list_return += [left[i]]
            i += 1
        elif left[i] >= right[j]:
            list_return += [right[j]]
            j += 1
            
    return list_return

def merge_sort(list_input):
    if len(list_input) <= 1:
        return list_input
    
    ## split course ##
    medium = len(list_input) // 2
    left = list_input[:medium]
    right = list_input[medium:]
    
    return merge(merge_sort(left), merge_sort(right))
```

그리고 6차례 테스트 결과
```
# Test Code

import random

list_test = random.sample(range(100), 15)

print(list_test)
print(merge_sort(list_test))

# Result

[9, 43, 67, 56, 84, 11, 18, 33, 31, 29, 80, 85, 52, 7, 66]
[7, 9, 11, 18, 29, 31, 33, 43, 52, 56, 66, 67, 80, 84, 85]

[79, 52, 20, 38, 64, 12, 4, 67, 17, 45, 61, 44, 8, 25, 41]
[4, 8, 12, 17, 20, 25, 38, 41, 44, 45, 52, 61, 64, 67, 79]

[25, 79, 28, 14, 1, 74, 39, 48, 29, 26, 17, 95, 52, 18, 89]
[1, 14, 17, 18, 25, 26, 28, 29, 39, 48, 52, 74, 79, 89, 95]

[99, 37, 73, 35, 19, 71, 81, 41, 25, 86, 36, 85, 13, 47, 61]
[13, 19, 25, 35, 36, 37, 41, 47, 61, 71, 73, 81, 85, 86, 99]

[83, 87, 17, 15, 90, 78, 69, 92, 47, 49, 0, 39, 41, 73, 65]
[0, 15, 17, 39, 41, 47, 49, 65, 69, 73, 78, 83, 87, 90, 92]

[48, 78, 99, 60, 45, 20, 27, 87, 19, 88, 32, 13, 2, 91, 5]
[2, 5, 13, 19, 20, 27, 32, 45, 48, 60, 78, 87, 88, 91, 99]
```

정렬이 잘 된다!<br/>


## 코딩 오류 수정 기록<br/>

코딩을 하는 과정에서 에러가 좀 발생했다. 수정 전 코드가 무엇이었고 그때문에 내가 무슨 에러를 발생시켰는지 기록도 해둬야지.<br/>

### 1. 파이썬의 몫 구하기 기호는 //이다. ###
```
# 수정 전 코드
medium = len(list_input) / 2
left = list_input[:medium]
right = list_input[medium:]

# 오류발생
TypeError: slice indices must be integers or None or have an __index__ method
```

medium 값이 float 값이 나와버려서 인덱싱에 오류가 발생했다.<br/>

### 2. 리스트는 리스트끼리 합칠 수 있다. ###
```
# 수정 전 코드
if left[i] < right[j]:
    list_return += left[i]
    i += 1
elif left[i] >= right[j]:
    list_return += right[j]
    j += 1


# 오류발생
TypeError: 'int' object is not iterable
```
`left[i]`와 `right[j]`를 반환시키면 int가 반환된다.<br/>
이렇게 되면 (list + int)형이 돼버려 에러가 발생한다.<br/>

```
list_return.append(left[i])
list_return.append(right[i])
```
리스트에 정수를 추가하는 형태로 고치거나,

```
list_return += [left[i]]
list_return += [right[i]]
```
리스트끼리 합치는 형태로 만들 수 있도록 만들어 해결했다.<br/>
