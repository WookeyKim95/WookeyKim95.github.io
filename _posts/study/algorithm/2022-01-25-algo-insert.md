---
layout: post
title:  "[알고리즘] 삽입정렬."
subtitle:   
date: 2022-01-25 11:20:46 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] 삽입정렬<br/>

## 삽입정렬이란?<br/>
---
> 1. 2~4번 과정은 배열에서 앞에서 두 번째에 있는 아이템, 즉 1번 인덱스부터 순차적으로 진행한다.
> 2. 아이템의 인덱스 값을 저장한다.
> 3. 아이템과 바로 1만큼 낮은 인덱스에 있는 아이템을 비교한다.
> 4. 아이템보다 1 만큼 낮은 인덱스에 있는 아이템의 값이 크다면, 현재 아이템과 1 만큼 낮은 인덱스에 있는 아이템을 바꾼다.
> 5. 아니라면, 중단하고 다음 인덱스에 대하여 2~4번 과정을 실시한다.
> 6. 정렬이 완료될때까지 2~5를 반복한다.

<br/>

### 에를들어서, 아래와 같은 배열이 있다고 해보자.<br/>
[1, 3, 5, 6, 2, 4]<br/>
<br/>
이 배열에 처음 삽입정렬을 실행하면 아래와 같은 과정이 일어난다.<br/>
<br/>
[1, 3, 5, 2, 6, 4] (맨 처음으로 3을 골랐지만, 3보다 낮은 인덱스에 있는 값은 1이다.)<br/>
[1, 3, 5, 2, 6, 4] (따라서, 과정을 실행하지 않고 다음 인덱스로 넘어간다.)<br/>
[1, 3, 5, 2, 6, 4] (따라서, 이 과정은 3, 5 까지는 과정을 실행하지 않는다.)<br/>
<br/>
그 다음으로 [1, 3, 5, 2, 6, 4] (3번 인덱스에 있는 아이템 2를 골랐다.)<br/>

- 2가 있는 인덱스인 3에서 1만큼 낮은 인덱스인 2에 있는 아이템의 값은 5이며, 5는 2보다 크므로, 5와 2의 위치를 바꾼다.<br/>
[1, 3, 2, 5, 6, 4]
- 다음으로, 2와 3을 비교한다. 3은 2보다 크므로, 3과 2의 위치를 바꾼다.<br/>
[1, 2, 3, 5, 6, 4]
- 마지막으로 1과 2를 비교한다. 1은 2보다 작으므로, 과정을 중단한다.<br/>
<br/>

따라서 [1, 2, 3, 5, 6, 4] 과 같아진다.<br/>
이후, 이전에 2 다음 아이템이었던 6에 대해서 과정을 실시한다.<br/>

마찬가지로 나머지 아이템 6, 4 에 대해서도 실행하면<br/>
[1, 2, 3, 5, 6, 4]는 [1, 2, 3, 5, 6, 4]가 되고, (6이 5보다 크므로 과정이 일어나지 않았음.)<br/>
마지막 순서는<br/>
[1, 2, 3, 5, 6, 4]가 [1, 2, 3, 4, 5, 6]으로 된다.

이렇게해서 정렬이 완료되었다.<br/>
<br/>

## 삽입정렬의 코드 구현<br/>
---
위에 적힌 과정을 통해 삽입정렬을 코드로 구현하면 아래와 같이 구현할 수 있다.

```
for i in range(1, len(data_list)):
    for i2 in range(i, 0, -1):
        if data_list[i2-1] > data_list[i2]:
            data_list[i2], data_list[i2-1] = data_list[i2-1], data_list[i2]
        else:
            break
```

코드를 구현하면서 오류를 많이 겪었는데,<br/>
```
# 첫 번째 오류 발생코드
for i in range(1, len(data_list)):
    a = data_list[i]
    for i2 in range(i-1, -1, -1): # i-1번 인덱스 부터 한칸 씩 역순으로 진행
        if a < data_list[i2]:
            tem = a
            data_list[i] = data_list[i2]
            data_list[i2] = tem
        else:
            break
```
이렇게 하면 2~4번 과정을 진행할 때 아이템의 자리를 바꾸기만 해야하는데 아이템의 값을 바꿔버리는 일이 일어난다.<br/>

그래서 아래와 같은 코드로 다시 짰다.<br/>
```
# 두 번째 오류 발생코드
for i in range(1, len(data_list)-1):
    for i2 in range(i, -1, -1):
        if data_list[i2-1] < data_list[i2]:
            data_list[i2], data_list[i2-1] = data_list[i2-1], data_list[i2]
        else:
            break
```
그런데 이렇게 해도 순서가 뒤죽박죽이 되길래 무슨일인가 해서 봤더니<br/>

**2~4번 과정을 위해서 비교를 해야하는 인덱스, 그러니까 i2-1의 값이 -1까지 넘어가버린다..**<br/>

**즉 마지막에 인덱스0의 아이템과 인덱스-1의 아이템을 비교해버리는 것인데, 인덱스-1은 배열의 맨 오른쪽 아이템을 의미하는 것이니 당연히 꼬일 수 밖에..**

**그리고 그 와중에 3번째 줄의 비교 부등호도 반대로 잘못 넣어놨었다...(부끄)**

그래서 완성한 코드가 위에 적어놓았던 코드이다.

```
import random

data_list = random.sample(range(100), 30)
print(data_list)

for i in range(1, len(data_list)):
    for i2 in range(i, 0, -1):
        if data_list[i2-1] > data_list[i2]:
            data_list[i2], data_list[i2-1] = data_list[i2-1], data_list[i2]
        else:
            break

print(data_list)
```
Spyder로 구동해보았다.<br/>

![출력](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-01-25_insert_1.jpg?raw=true)
<br/>
음! 잘나온다.

![출력](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-01-25_insert_2.jpg?raw=true)
<br/>
<br/>
### 시간복잡도<br/>

버블정렬과 비슷한 양상을 보이는 것 같다.<br/>
이것 역시 최선의 경우는 완전 정렬이 되어있는 경우로 탐색 횟수가 (배열의 길이-1) 일테니
시간복잡도는 $ O({n}) $이고, <br/>
<br/>
최악의 경우는 역정렬이 되어있는 경우로 탐색 횟수는 1부터 (배열의 길이-1) 까지의 합이므로,
시간복잡도는 $ O({n}^2) $이다.<br/>







