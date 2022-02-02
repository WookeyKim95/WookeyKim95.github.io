---
layout: post
title:  "[자료구조] 이진트리(Binary Tree) 1"
subtitle:   "개요"
date: 2022-02-02 12:11:23 +0900
categories: study
tags: data_structure
comments: true
related_posts:
    - /_posts/study/2022-02-02-data-binary_tree_2.md
    
---

## 이진트리(Binary Tree)란?<br/>

![이진트리](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-02_binary_tree_1.jpg?raw=true)
<br/>

트리는 자료구조의 일종으로, 노드(Node)와 브랜치(Branch)를 사용해서 그래프가 사이클을 이루지 않도록 배치한 구조이다.<br/>

이진트리는 트리의 일종으로, 배열과 비슷한 기능을 하면서 배열보다 빠른 검색을 사용하기 위해서 사용하는 구조이다.<br/>
그래서 검색이나 탐색을 위한 알고리즘 구현을 위해서 많이 사용된다.<br/>

## 이진트리의 코드 구현<br/>

### 노드 형성하기<br/>

```
# 노드 클래스 만들기
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
```
위의 코드는 값을 저장하고 왼쪽 자식노드와 오른쪽 자식노드를 형성하기 위한 코드이다.<br/>

```
# 노드 2개 생성

Node_1 = Node(1)
Node_2 = Node(2)
```
위의 class코드가 있는 상태에서 간단하게 테스트코드를 돌려서 노드 2개를 생성해주면 아래 그림과 같은 상태가 된다.<br/>

![노드두개](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-02_binary_tree_2.jpg?raw=true)
<br/>

여기서, `Node_1.left = Node_2`를 한다면?<br/>

![2는1의자식노드](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-02_binary_tree_3.jpg?raw=true)
<br/>

노드2가 노드1의 왼쪽 자식노드가 되었다!<br/>
여기서 다시 `Node_1.left = None`을 한다면 노드1과 노드2는 다시 분리된다. 브랜치를 없앤다고 노드가 사라지는 것은 아니다.<br/>

### 이진트리에 노드 삽입하기<br/>

```
class NodeMgmt:
    def __init__(self, head):
        self.head = head
```
기능을 추가하기에 앞서, 어떤 이진 트리에 대해서 데이터 삽입, 탐색, 삭제를 원활하게 하기 위해서 이진 트리에 대한 클래스를 추가하고, 그 클래스 내에서 기능이 동작하도록 위의 코드를 작성하였다.<br/>
여기서 self.head가 이진트리의 루트노드역할을 하게된다.<br/>

```
class NodeMgmt:
    def __init__(self, head):
        self.head = head

# 이진 탐색 트리에 데이터 넣기

    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
        	    if self.current_node.left != None:
        	        # 비교 대상 교체
        	        self.current_node = self.current_node.left
        	    else: # 빈 곳에 데이터 삽입
        	        self.current_node.left = Node(value)
        	        break
            else:
        	    if self.current_node.right != None:
        	        self.current_node = self.current_node.right
        	    else: # 빈 곳에 데이터 삽입
        	        self.current_node.right = Node(value)
        	        break
```
그 후에 위와 같이 데이터 삽입을 위한 코드를 넣어줄 수 있다.<br/>
과정은 아래와 같다.<br/>

1. 루트노드(self.head)를 현재 탐색노드로 지정한다.<br/>
2. 현재 탐색노드(self.current_node.value)의 값과 넣고자 하는 값(value)을 비교한다.<br/>
(value의 값이 더 작다면 3번으로, value의 값이 더 크다면 4번으로 이동한다.)
3. value의 값이 더 작다면 왼쪽으로 이동해야하므로 왼쪽에 자식노드가 있는지 검색한다.<br/>
(자식노드가 있다면 self.current_node를 그 자식노드로 이동한 후 2번 과정으로 돌아간다.)
4. value의 값이 더 크다면 오른쪽으로 이동해야하므로 오른쪽에 자식노드가 있는지 검색한다.<br/>
(자식노드가 있다면 self.current_node를 그 자식노드로 이동한 후 2번 과정으로 돌아간다.)
5. 자식노드가 없다면 self.current_node.left(혹은 right)를 새로운 노드로 추가해준다.<br/>

코드의 형태나 전체적 과정이 링크드리스트를 형성하는 과정과 비슷하다.<br/>
다만 링크드리스트는 선이 하나로 연결되어있는 느낌이라면, 이진 트리는 노드하나에 여러 개의 노드가 연쇄적으로 연결되는 느낌?<br/>

### 이진트리에서 값 탐색하기<br/>

```
# 이진 트리 탐색하기
    def search(self, value):
        self.current_node = self.head # 순회 시작점 : head
        while self.current_node != None:
	        if self.current_node.value == value:
	            return True # 찾으면 True 리턴하던지, 값 리턴하던지
	        elif value < self.current_node.value:
	            self.current_node = self.current_node.left
	        elif value > self.current_node.value:
	            self.current_node = self.current_node.right
        return False
```

현재 값보다 더 큰 값은 오른쪽에, 더 작은 값은 왼쪽에 배치되는 것이 이진트리이므로 이를 이용해서 빠르게 검색을 할 수 있도록 한다.<br/>
새로운 값은 insert하는 과정과 비슷하다. 하지만 값이 없다면 무한루프를 빠져나와서 False를 return하도록 코드를 짜면 된다.<br/>

### 삽입, 탐색 테스트<br/>

```
# 노드 클래스 만들기
class Node:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


class NodeMgmt:
    def __init__(self, head):
        self.head = head

# 이진 탐색 트리에 데이터 넣기

    def insert(self, value):
        self.current_node = self.head
        while True:
            if value < self.current_node.value:
        	    if self.current_node.left != None:
        	        # 비교 대상 교체
        	        self.current_node = self.current_node.left
        	    else: # 빈 곳에 데이터 삽입
        	        self.current_node.left = Node(value)
        	        break
            else:
        	    if self.current_node.right != None:
        	        self.current_node = self.current_node.right
        	    else: # 빈 곳에 데이터 삽입
        	        self.current_node.right = Node(value)
        	        break

# 이진 트리 탐색하기
    def search(self, value):
        self.current_node = self.head # 순회 시작점 : head
        while self.current_node != None:
	        if self.current_node.value == value:
	            return True # 찾으면 True 리턴하던지, 값 리턴하던지
	        elif value < self.current_node.value:
	            self.current_node = self.current_node.left
	        elif value > self.current_node.value:
	            self.current_node = self.current_node.right
        return False
```

따라서, 지금까지 작성된 코드는 위와 같다. 아래의 테스트코드를 구동해보고 결과를 보자.<br/>

```
import random
random_list = []
for i in range(25):
    random_list.append(random.randint(1,500))
print(random_list)

Head = Node(250)
binary_tree = NodeMgmt(Head)

for j in random_list:
    binary_tree.insert(j)

for j in random_list:
    if binary_tree.search(j) == False:
        print(j, '해당 값 없음.')
    else:
        print(j)

print('서치완료')
```
값 250을 가진 노드를 루트노드로 하는 이진트리를 형성하고, 1부터 499까지의 무작위 정수 25개를 이진트리에 넣도록 했다.<br/>

```
[189, 371, 171, 143, 90, 94, 376, 379, 340, 379, 492, 121, 304, 476, 330, 10, 412, 229, 145, 311, 433, 349, 368, 163, 367]
```
random_list는 위와 같이 형성되었으며

```
189
371
171
143
90
94
376
379
340
379
492
121
304
476
330
10
412
229
145
311
433
349
368
163
367
서치완료
```
이진트리에 값이 잘 들어간 것을 확인할 수 있다.<br/>
다음 포스팅에선 이진트리 내 노드 삭제에 대해서 다루겠다.<br/>

**2편에서 계속..**