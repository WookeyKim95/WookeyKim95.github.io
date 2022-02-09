---
layout: post
title:  "[자료구조] 큐(Queue)"
subtitle:   "개요"
date: 2022-01-22 09:10:21 +0900
tags: data_structure
categories: study
comments: true
related_posts:

---


## 큐란 무엇인가?<br/>

큐는 배열 자료구조의 하나로, 흔히 FIFO(First In, First Out)<br/>
즉, 먼저 들어간 자료가 먼저 반환되는 자료구조이다.<br/>

### [] <br/>

이렇게 리스트를 형성한 뒤, 순서대로 3, 'funcoding', 1 을 넣어주면<br/>

### [3, 'funcoding', 1] <br/>

이 되며, 자료를 모두 반환한다면<br/>

3<br/>
'funcoding'<br/>
1<br/>

이런 순서대로 반환이 되는 자료구조이다.<br/>
<br/>

## 지금까지 나는..<br/>

난 지금까지 큐를 사용했던 방법이 리스트를 하나 만들고<br/>
데이터를 넣을 때는 list.append(),<br/>
데이터를 꺼낼 때는 list.pop(0)<br/>
을 적극적으로 사용했다.<br/>
<br/>
일반적인 자료에서 FIFO형을 구현하고 싶은 거였으면 그렇다 했겠지만<br/>
오늘 새로운 내용을 배우고나니 또 차이가 있다는게 느껴진다..<br/>

## 오늘 새로 배운 내용은?

```
import queue

data_queue = queue.Queue() # 일반적인 FIFO형태의 큐
data_queue_2 = queue.LifoQueue() # LIFO 형태의 큐
data_queue_p = queue.PriorityQueue() # 우선순위를 배당하는 큐
```
<br/>

### queue.Queue()<br/>

`data_queue = queue.Queue()`<br/>
우리가 일반적으로 알고 있는 큐이다.<br/>
내가 위에서 언급했던 리스트에 append하고, pop(0)을 하면서 나름 구현할 수 있는 형태이다.<br/>
하지만 차이가 있었던 점이, queue.Queue()를 통해서 만들어진 큐를 print해보니까<br/>

```
data_queue = queue.Queue()
print(data_queue)
# [자료1, 자료2] 이런 형태로 나올 줄 알았는데..
# 출력결과 : queue.Queue object at 0x0000020BB2FCCEE0
```
<br/>
이렇게 출력되더라..<br/>
부족한 CS지식을 갖다가 추측을 해보자면<br/>
아예 메모리 위치를 잡고 넉넉하게 할당해서 자료를 넣어서 보관하는 형태인 것 같았다.<br/>
마치 C언어에서 int a = 0 라고하면<br/>
'a 는 곧 0이다.' 가 아니라<br/>
'a에 대해 어느 메모리가 할당됐고 그 안에 0이 있어.'를 나타내는 느낌이랄까..<br/>


그래서 더 검색을 해보니<br/>
`data_queue = queue.Queue(3)`<br/>
이렇게 하면 3자리만 있는 큐를 만들 수 있다는 것을 알 수 있었다.<br/>
<br/>
즉, 지금까지 알고 있던 것과는 달리 단순한 리스트구조가 아니라는 것이다.<br/>
또한, 리스트와는 메모리 할당량, 구동 속도 면에서도 차이가 있을 것이라 생각된다.<br/>
<br/>

### queue.LifoQueue()<br/>

`data_queue = queue.LifoQueue()`<br/>
Last in First Out의 형태를 가지는 큐로, 스택과 같다고 보여진다.<br/>

따라서 코드를 아래와 같이 작성하면<br/>
```
data_queue_2 = queue.LifoQueue()

data_queue_2.put('funcoding')
data_queue_2.put(1)

print(data_queue_2.get())
# 출력결과 : 1
print(data_queue_2.get())
# 출력결과 : 'funcoding'
```
이렇게 된다!<br/>

### queue.PriorityQueue()<br/>

신기하게도 queue 라이브러리 안에 우선순위를 사용하는 큐도 있었다.<br/>
다만 이 큐는 배운 내용으로는<br/>
```
(1, 'hello')
(2, 'it's me')
(3, 'wow')
(4, 'how are you?')
```
<br/>
이렇게 자료가 튜플형이고, 0번 인덱스에 우선순위가, 1번 인덱스에 자료가 들어가는 형태이다.<br/>

```
data_queue = queue.PriorityQueue()

data_queue.put(('k', 'hello'))
data_queue.put((2, 'it's me'))
data_queue.put((3, 'wow'))
data_queue.put((4, 'how are you?'))
```
<br/>
혹시 저렇게 하면 어떻게 되지? 하면서 1을 'k'로 바꿔봤더니 역시나 TypeError..<br/>

**0번 인덱스에는 모두 문자형을 쓰거나, 모두 정수형을 써야 에러가 안난다!**<br/>
고로 0번 인덱스에는 정수형을 써놔야 맘이  편할 것 같다.<br/>

**아참! 그리고 낮은 값을 가지는 것이 높은 우선순위를 가진다!**<br/>

따라서<br/>
```
data_queue = queue.PriorityQueue()

data_queue.put((1, 'hello'))
data_queue.((4, 'it's me'))
data_queue.((2, 'wow'))
data_queue.((3, 'how are you?'))

print(data_queue.get())
print(data_queue.get())
print(data_queue.get())
print(data_queue.get())

# 출력결과
(1, 'hello')
(2, 'it's me')
(3, 'wow')
(4, 'how are you?')
```
<br/>

0번 인덱스가 1, 2, 3, 4 인 순서대로 출력되었다!<br/>
다만, 튜플형 자료구조가 그대로 출력되기 때문에 인덱스가 아닌 자료만을 원한다면<br/>

```
print(data_queue.get()[1])
```
<br/>
이렇게 조절해야할 것 같다.<br/>
