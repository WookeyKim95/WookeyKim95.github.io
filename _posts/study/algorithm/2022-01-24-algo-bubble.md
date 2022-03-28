---
layout: post
title:  "[알고리즘] 버블정렬."
subtitle:   
date: 2022-01-24 09:14:35 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] 버블정렬<br/>

## 버블정렬이란?<br/>
   
> 1. 배열에서 앞에서부터 순서대로 두 인덱스를 비교하면서 앞의 값이 더 크면 두 인덱스의 자리를 바꾼다.   
> 2. 1번의 과정을 배열의 처음부터 끝 까지 반복한다.   
> 3. 배열의 정렬이 완료될 때 까지 1~2를 반복한다.   

<br/>

### 에를들어서, 아래와 같은 배열이 있다고 해보자.<br/>
[1, 3, 5, 6, 2, 4]<br/>
<br/>
이 배열에 버블정렬을 실행하면 아래와 같은 과정이 일어난다.<br/>
<br/>
[1, 3, 5, 2, 6, 4] (6이 2보다 더 크므로 두 인덱스의 자리를 바꾼다.)<br/>
[1, 3, 5, 2, 4, 6] (6이 4보다 더 크므로 두 인덱스의 자리를 바꾼다.)<br/>
<br/>
아직 완벽하게 오름차순 정렬이 완료되지 않았으므로 6을 제외하고 배열의 처음부터 다시 정렬한다.
<br/>
[1, 3, 2, 5, 4, 6] (5가 2보다 더 크므로 두 인덱스의 자리를 바꾼다.)<br/>
[1, 3, 2, 4, 5, 6] (5가 4보다 더 크므로 두 인덱스의 자리를 바꾼다.)<br/>
<br/>
다시 처음으로 돌아가서<br/>
[1, 2, 3, 4, 5, 6] (3이 2보다 더 크므로 두 인덱스의 자리를 바꾼다.)<br/>
[1, 2, 3, 4, 5, 6] (4가 3보다 더 크므로 자리를 바꿀 필요가 없다.)<br/>

이렇게해서 정렬이 완료되었다.<br/>
<br/>
<br/>
버블정렬을 구현하는 데에 핵심은<br/>
1. 인접한 두 인덱스를 비교할 것.
2. 배열 전체를 1번 편도할 때 마다 알고리즘을 실행할 배열의 길이는 1씩 줄어든다.

이를 염두하면서 이 과정을 파이썬 코드로 나타내보면<br/>
```
import random

def bubblesort(data):
    for i1 in range(len(data) - 1):
        swap = False # 이미 자리 바꾸기를 했는지 여부를 따짐.
        # 이미 자리를 바꿨다면 생략하는 것이 구동속도가 더 빠르니.
        for i2 in range(len(data) - i - 1):
            if data[i2] > data[i2 + 1]:
                data[i2], data[i2 + 1] = data[i2 + 1], data[i2]
                swap = True
        
        if swap == False:
            break


data_list = random.sample(range(100), 50)
print(data_list)
bubblesort(data_list)
print(data_list)
```
한번 시험을 해볼까?<br/>

input data<br/>

![입력](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-01-24_bubble_1.jpg?raw=true)
<br/>
<br/>
output data<br/>

![출력](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-01-24_bubble_2.jpg?raw=true)
<br/>
Spyder에서 실행시켜보았다. 음! 잘나온다.<br/>

### 시간복잡도

버블 정령의 최선의 경우는 <br/>
완전히 정렬이 되어있는경우<br/>
[1, 2, 3, 4, 5, 6]<br/>
위와 같다면, 탐색 횟수는 배열의 길이 - 1 즉, n-1 으로 끝날 것이다.<br/>
<br/>
반면에 최악의 경우는<br/>
[6, 5, 4, 3, 2, 1]<br/>
완벽하게 역순으로 되어있는 경우일 것이다.<br/>
이 경우는 1부터 n-1 까지 자연수의 합 만큼 탐색 횟수가 늘어날 것이다.<br/>
<br/>
예를들어, 위에 나타난 배열의 경우에는 배열의 길이가 6이라면<br/>
5 + 4 + 3 + 2 + 1 = 총 15번,<br/>
<br/>
그렇다면 배열의 길이가 n 이라면<br/>

$ \frac{n(n-1)}{2} $<br/>

위와 같이 나타난다.<br/>
이 때, 시간 복잡도의 특성은 최고차항만을 따지므로,<br/>
Big O 표기법으로 나타낸다면<br/>

$ O({n}^2) $<br/>

이렇게 될 것이다.<br/>


