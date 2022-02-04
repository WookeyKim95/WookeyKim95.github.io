---
layout: post
title:  "[자료구조] 이진트리(Binary Tree) 3"
subtitle:   "개요"
date: 2022-02-04 13:10:45 +0900
categories: study
tags: data_structure
comments: true
related_posts:
    - /_posts/study/2022-02-02-data-binary_tree_1.md
    - /_posts/study/2022-02-03-data-binary_tree_2.md
---

## 오류의 발생<br/>

1, 2 편에서 이진트리의 데이터 삽입, 서치, 삭제 코드를 구현했지만, 삭제 코드를 실행한 이후 다시 서치를 하는 과정에서 무한루프에 빠지거나, 삭제된 데이터가 서치되는 오류가 발생했다.<br/>

오류가 발생한 상태에서 전체 코드는 아래와 같다.<br/>

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
        	        return
            elif value > self.current_node.value:
        	    if self.current_node.right != None:
        	        self.current_node = self.current_node.right
        	    else: # 빈 곳에 데이터 삽입
        	        self.current_node.right = Node(value)
        	        return
            elif value == self.current_node.value:
                print('데이터가 이미 있음.')
                return

# 이진 트리 탐색하기
    def search(self, value):
        self.current_node = self.head # 순회 시작점 : head
        while self.current_node != None:
            if self.current_node.value == value:
	            return True # 찾으면 True 리턴하던지, 값 리턴하던지
            elif value < self.current_node.value:
                if type(self.current_node.left) == None:
                    print('해당 값 없음.')
                    return False
                else:
                    self.current_node = self.current_node.left
            elif value > self.current_node.value:
                if type(self.current_node.right) == None:
                    print('해당 값 없음.')
                    return False
                else:
                    self.current_node = self.current_node.right
        return False

# Leaf Node 삭제하기 (맨 밑에 자식노드 없는 노드)
# 그냥 그 노드를 가리키지 않게 한다.

# 자식노드가 한개인 노드 삭제하기.
# 삭제할 Node의 부모노드가 삭제할 노드의 자식노드를 가리키도록 한다.

# 자식노드가 두개인 노드 삭제하기.
# 1. 삭제할 Node의 오른쪽 자식 중, min을 삭제할 Node의 부모노드가 가리키도록 한다.
# 2. 삭제할 Node의 왼쪽 자식 중, max를 삭제할 Node의 부모노드가 가리키도록 한다.
# -> 이렇게 하면 Tree 구조의 규칙이 깨지지 않음.

    def delete(self, value):
        searched = False
        self.current_node = self.head
    
        # 해당 값을 가지는 노드를 찾기
        while self.current_node != None:
            if self.current_node.value == value:
                searched = True
                break
            elif value < self.current_node.value:
    	        self.parent = self.current_node
    	        self.current_node = self.current_node.left
            else:
                self.parent = self.current_node
                self.current_node = self.current_node.right
        if searched == False:
            return False

# 이후부터 Case를 분리해서 삭제코드 작성.

# Case 1 : 대상 노드가 Leaf Node일 때.
        if self.current_node.left == None and self.current_node.right == None:
            if value < self.parent.value:
                self.parent.left = None # 부모의 자식노드가 삭제되는 원리.
            else:
                self.parent.right = None

# Case 2 : 대상 노드가 자식노드가 1개일 때
        elif self.current_node.left == None or self.current_node.right == None:
            if self.current_node.left != None: # 그럼 오른쪽 노드가 없는경우.
            	if value < self.parent.value:
                    self.parent.left = self.current_node.left
            	else:
                    self.parent.right = self.current_node.left
            if self.current_node.right != None: # 그럼 왼쪽 노드가 없는경우.
            	if value < self.parent.value:
                    self.parent.left = self.current_node.right
            	else:
                    self.parent.right = self.current_node.right

# Case 3 : 대상 노드가 자식 노드가 2개일 때
# 1. 삭제할 Node의 오른쪽 자식 중, 작은 값을 삭제할 Node의 부모노드가 가리키도록 한다.

        elif self.current_node.left != None and self.current_node.right != None:
            if value < self.parent.value:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node

                # 순회과정
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left

        	    # 순회 후 branch를 조절.
                if self.change_node.right != None:
        	        self.change_node_parent.left = self.change_node.right
                else:
        	        self.change_node_parent.left = None
                self.parent.left = self.change_node
                self.change_node.right = self.current_node.right
                self.change_node.left = self.current_node.left

# 2. 삭제할 Node의 왼쪽 자식 중, max를 삭제할 Node의 부모노드가 가리키도록 한다.

            elif value > self.parent.value:
            	self.change_node = self.current_node.left
            	self.change_node_parent = self.current_node
            	# 순회과정
            	while self.change_node.right != None:
            	    self.change_node_parent = self.change_node
            	    self.change_node = self.change_node.right
    
                # 순회 후 branch를 조절하기.
            	if self.change_node.left != None:
            	    self.change_node_parent.right = self.change_node.left
            	else:
            	    self.change_node_parent.right = None
            
            	self.parent.right = self.change_node
            	self.change_node.left = self.current_node.left
            	self.change_node.right = self.current_node.right
        return
```

그리고 테스트 코드는 아래와 같았다.<br/>

```
import random

random_list = []
for i in range(25):
    random_list.append(random.randint(1,500))
print(random_list)

Head = Node(random_list[0])
binary_tree = NodeMgmt(Head)

for j in random_list:
    binary_tree.insert(j)

for j in random_list:
    if binary_tree.search(j) == False:
        print(j, '해당 값 없음.')
    else:
        print(j)

print('서치완료')

for j in random_list[2:(len(random_list)//2)]:
    if binary_tree.delete(j) == False:
        print(j, '해당 값 없음.')
    else:
        print(j, '삭제')

print('삭제완료')

for j in random_list:
    if binary_tree.search(j) == False:
        print(j, '해당 값 없음.')
    else:
        print(j)

print('2차 서치완료')
```

서치까지는 정상적으로 되지만, 삭제하는 과정에서 아직 삭제가 안됐는데도 해당 데이터를 찾지 못하거나, 삭제를 했는데도 데이터가 검색되는 오류가 발생한다.<br/>
이말인 즉슨, 삭제를 하는 코드 쪽에 오류 발생원인이 있는 것으로 생각했다.<br/>

## 오류 추적<br/>

오류가 어디서 발생하는지 찾기 위해 노드를 이동할 때 마다 찾고자 하는 값, 그리고 현재 노드의 값, 그리고 어디로 이동하는지 출력을 해보았다.<br/>
그런데 재밌는 현상이 발생했다. 무한루프에 빠질 때 상태가 공통적으로 아래와 같았다.

- 현재 노드의 값 < 찾고자 하는 값
- 그래서 오른쪽 자식노드로 이동.

오른쪽 자식노드로 이동했지만, 다시 제자리로 돌아오는 현상이 발생하는 것이다.<br/>
테스트 코드를 30회 이상 실행했지만, 왼쪽 자식노드로 이동하는 과정에서 무한루프는 발견되지 않았다.<br/>
삭제코드를 실행한 이후에 발생하는 것을 보고 삭제코드 쪽을 살펴 보기로 했다.<br/>

### Test Case

```
list = [1,4,3,9,11,6,7,8,2,12,5,10]

Root = Node(7)
Binary = NodeMgmt(Root)

for i in list:
    Binary.insert(i)
    
print('추가 완료')

for i in list:
    Binary.search(i)
    
print('서치 완료')
```

list에 있는 데이터를 넣는 과정이다.<br/>
루트노드에 7을 넣고, 리스트에 있는 값을 순서대로 트리에 insert하였다.<br/>

![기대 결과_1](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-03_binary_tree_1.jpg?raw=true)

기대하는 결과는 위 그림과 같았고, 구동 결과도 요구에 만족하였다.<br/>
<br/>
그리고 필자가 생각하기에 자식노드가 2개 인 경우를 삭제하는 과정에서 오류가 발생하는 것으로 추측하였는데, 저 그림에서 대표적인 예시로 11이 있다.<br/>
따라서, `binary.delete(11)`을 실행하였다.<br/>
그리고 다시 서치했을 때 기대할 수 있는 결과는 아래 그림과 같다.<br/>

![기대 결과_2](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-03_binary_tree_2.jpg?raw=true)

하지만 실행결과는?<br/>
10에서 오른쪽 자식노드로 이동하면서 12가 서치되어야하지만, 12를 발견하지 못하는 현상이 발생했다.<br/>
즉, 새로운 자식노드 연결이 잘못된 것 같다.<br/>

## 오류 해결 과정<br/>

알고리즘 강의도 다시 들어보고, [강의에서 참고한 링크의 포스트](https://ejklike.github.io/2018/01/09/traversing-a-binary-tree-1.html)도 참고해본결과, 현재 내 코드랑 다른 점을 찾을 수 있었다.<br/>

```
# 오류 발생 코드

            elif value > self.parent.value:
            	self.change_node = self.current_node.left
            	self.change_node_parent = self.current_node
            	# 순회과정
            	while self.change_node.right != None:
            	    self.change_node_parent = self.change_node
            	    self.change_node = self.change_node.right
    
                # 순회 후 branch를 조절하기.
            	if self.change_node.left != None:
            	    self.change_node_parent.right = self.change_node.left
            	else:
            	    self.change_node_parent.right = None
            
            	self.parent.right = self.change_node
            	self.change_node.left = self.current_node.left
            	self.change_node.right = self.current_node.right
        return


# 오류 수정 후 코드

            elif value > self.parent.value:
            	self.change_node = self.current_node.left
            	self.change_node_parent = self.current_node.right
            	# 순회과정
            	while self.change_node.left != None:
            	    self.change_node_parent = self.change_node
            	    self.change_node = self.change_node.left
    
                # 순회 후 branch를 조절하기.
            	if self.change_node.right != None:
            	    self.change_node_parent.left = self.change_node.right
            	else:
            	    self.change_node_parent.left = None
            
            	self.parent.right = self.change_node
            	self.change_node.right = self.current_node.right
            	self.change_node.left = self.current_node.left
        return
```

예상대로, 오류는 자식 노드가 2개인 노드를 삭제하며, 해당 값이 부모 노드의 값보다 큰 경우를 삭제할 경우에 있었다.<br/>

신기한 점은, 기존 노드를 새로운 노드를 지정할 때, 즉 순회과정과 순회 후 branch를 조절하는 과정에서 자식 노드가 2개인 노드를 삭제하며, 해당 값이 부모 노드의 값보다 작은 경우를 삭제할 경우와 같은 코드를 사용하고 있었다.<br/>
실제로 고쳐보니 오류 발생 빈도가 줄었다.<br/>

```
# 이진 트리 탐색하기
    def search(self, value):
        self.current_node = self.head
        # 해당 값을 가지는 노드를 찾기
        while self.current_node:
            if self.current_node.value == value:
                return True
            elif value < self.current_node.value:
                self.parent = self.current_node
                self.current_node = self.current_node.left
                if self.parent == self.current_node:
                    self.current_node.left = None
                    break
            else:
                self.parent = self.current_node
                self.current_node = self.current_node.right
                if self.parent == self.current_node:
                    self.current_node.right = None
                    break
            
            if self.parent == self.current_node:
                break

        return False

    def delete(self, value):
        if not self.search(value):
            return
    ...
```

그리고 탐색코드와 삭제코드 내에 있는 탐색코드가 중복되기 때문에 통합시켰다.<br/>
어차피 값을 찾고 삭제하거나, 값을 찾고 return하는 것은 똑같기 때문에..<br/>
그리고 아래에 있는 코드는 **일단은** 무한루프에 빠지는 것을 방어하기 위한이다.
```
if self.parent == self.current_node:
    self.current_node.left = None
    break

if self.parent == self.current_node:
    self.current_node.right = None
    break
```

삭제 과정에서 새로운 노드를 잘 연결해주어야 하는데 자기 자신으로 연결되어버려 무한루프에 빠지는 경우가 생겨서 이동을 했더니 자기 자신으로 돌아오면 None으로 바꾼뒤 break해버리는 구조이다.<br/>

우선 이렇게 하니 삭제되었기 때문에 검색이 되어서는 안되는 값이 검색되는 오류는 사라졌다.<br/>
하지만 위에 있는 방어코드 때문에 데이터의 유실이 생긴다.<br/>

어떻게 해결할 수 있을까 생각했지만, 
별수 있나? 이젠 직접 그려서 파악해볼 수 밖에..<br/>

```
# Test Case
[307, 120, 52, 378, 95, 84, 342, 46, 122, 227, 367, 369, 48, 409, 24, 64, 225, 369, 224, 290, 50, 363, 318, 381, 221]

```
![기대 결과_3](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-04_binary_tree_1.jpg?raw=true)

위 테스트케이스에서 기대할 수 있는 트리 형태는 위와 같다.<br/>

```

# 구동 결과
307
120
52
378
95
84
342
46
122
227
367
369
48
409
24
64
225
369
224
290
50
363
318
381
221
서치완료

52 삭제
378 삭제
95 삭제
84 삭제
342 삭제
46 삭제
122 삭제
227 삭제
367 삭제
369 삭제
삭제완료

307
120
52 해당 값 없음.
378 해당 값 없음.
95 해당 값 없음.
84 해당 값 없음.
342 해당 값 없음.
46 해당 값 없음.
122 해당 값 없음.
227 해당 값 없음.
367 해당 값 없음.
369 해당 값 없음.
48
409
24
64
225 해당 값 없음.
369 해당 값 없음.
224 해당 값 없음.
290
50 해당 값 없음.
363 해당 값 없음.
318
381
221
2차 서치완료
```
삭제 후 2차 서치결과 삭제되어야 할 값은 정상적으로 서치가 안됐지만, 225, 224, 50, 363은 모두 삭제대상이 아니므로 서치가 되어야하는데 서치가 안됐다. 즉, 소실되어버렸다.<br/>

여기서 주로 소실되는 데이터가 부모노드와 연결이 끊겨서 탐색을 못하는 경우가 많았다.<br/>
주로 자식노드가 2개인 경우 소실이 되는 경우가 발생했고, 새로운 노드를 연결하는 과정에서 노드 연결 방법이 잘못되어 무한루프가 발생하게 되었던 것이었다.<br/>

그러면 어느 과정에서 그랬는가?<br/>
맨 처음에 self.change_node와 self.change_node_parent를 같은 값으로 설정한 다음에 순회할 때 마다 값이 바뀌도록 해는데, 값이 바뀌지 않은 채로 순회가 끝났기 때문에 발생하는 것이었다.<br/>

### 삭제코드 수정<br/>

```
# Case 3 : 대상 노드가 자식 노드가 2개일 때
# 1. 삭제할 Node의 오른쪽 자식 중, 제일 작은 값을 삭제할 Node의 부모노드가 가리키도록 한다.

        elif self.current_node.left != None and self.current_node.right != None:
            if value < self.parent.value:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node.right

                # 순회과정
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left

        	    # 순회 후 branch를 조절.
                if self.change_node_parent != self.change_node:
                    if self.change_node.right != None:
        	            self.change_node_parent.left = self.change_node.right
                    else:
        	            self.change_node_parent.left = None
                    
                    self.parent.left = self.change_node
                    self.change_node.right = self.current_node.right
                    self.change_node.left = self.current_node.left
                
                else:
                    if self.change_node.right != None:
                        self.change_node_parent.left = self.change_node.right
                    else:
                        self.change_node_parent.left = None
                    
                    self.parent.left = self.change_node
                    self.change_node.left = self.current_node.left


# 2. 삭제할 Node의 왼쪽 자식 중, max를 삭제할 Node의 부모노드가 가리키도록 한다.

            elif value > self.parent.value:
                self.change_node = self.current_node.left
                self.change_node_parent = self.current_node.left
            	# 순회과정
                while self.change_node.right != None:
            	    self.change_node_parent = self.change_node
            	    self.change_node = self.change_node.right
    
                # 순회 후 branch를 조절하기.
                if self.change_node_parent != self.change_node:
                    if self.change_node.left != None:
                        self.change_node_parent.right = self.change_node.left
                    else:
                        self.change_node_parent.right = None
                    
                    self.parent.right = self.change_node
                    self.change_node.right = self.current_node.right
                    self.change_node.left = self.current_node.left # 오류발생지점 수정 요망
                
                else:
                    if self.change_node.left != None:
                        self.change_node_parent.right = self.change_node.left
                    else:
                        self.change_node_parent.right = None
                    
                    self.parent.right = self.change_node
                    self.change_node.right = self.current_node.right

        return
```
그래서 위와 같이 만약에 순회가 바로 끝나버려서, `if self.change_node_parent == self.change_node:`
인 경우를 추가하였다. 위에는 `else:`로 표현되었다.<br/>

```
# 구동 결과
52 삭제
378 삭제
95 삭제
84 삭제
342 삭제
46 삭제
122 삭제
227 삭제
367 삭제
369 삭제
삭제완료
307
120
52 해당 값 없음.
378 해당 값 없음.
95 해당 값 없음.
84 해당 값 없음.
342 해당 값 없음.
46 해당 값 없음.
122 해당 값 없음.
227 해당 값 없음.
367 해당 값 없음.
369 해당 값 없음.
48
409
24
64
225
369 해당 값 없음.
224
290
50
363
318
381
221
2차 서치완료
```

그리고 테스트결과는... 소실 발생이 없었다!<br/>

마지막으로 조마조마하는 마음으로 랜덤 변수를 넣어 테스트 해보았다.<br/>
```
# Test Case 1

[151, 51, 13, 411, 171, 358, 389, 292, 388, 133, 83, 112, 365, 318, 475, 200, 64, 318, 423, 409, 151, 27, 216, 268, 120]

# 1차 서치
151 51 13 411 171 358 389 292 388 133 83 112 365 318 475 200 64 318 423 409 151 27 216 268 120

# 삭제 대상
13 삭제
411 삭제
171 삭제
358 삭제
389 삭제
292 삭제
388 삭제
133 삭제
83 삭제
112 삭제
삭제완료

151 51
13 해당 값 없음.
411 해당 값 없음.
171 해당 값 없음.
358 해당 값 없음.
389 해당 값 없음.
292 해당 값 없음.
388 해당 값 없음.
133 해당 값 없음.
83 해당 값 없음.
112 해당 값 없음.
365 318 475 200 64 318 423 409 151 27 216 268 120 2차 서치완료
```
테스트 케이스 1 통과

```
# Test Case 2

[419, 151, 381, 389, 89, 1, 314, 419, 184, 270, 333, 118, 420, 389, 415, 234, 307, 495, 449, 105, 303, 32, 463, 187, 197]

# 1차 서치
419 151 381 389 89 1 314 419 184 270 333 118 420 389 415 234 307 495 449 105 303 32 463 187 197 서치완료

# 삭제 대상
381 삭제
389 삭제
89 삭제
1 삭제
314 삭제
419 삭제
184 삭제
270 삭제
333 삭제
118 삭제
삭제완료

419 151
381 해당 값 없음.
389 해당 값 없음.
89 해당 값 없음.
1 해당 값 없음.
314 해당 값 없음.
419 184 해당 값 없음.
270 해당 값 없음.
333 해당 값 없음.
118 해당 값 없음.
420 389 해당 값 없음.
415 234 307 495 449 105 303 32 463 187 197 2차 서치완료
```
테스트 케이스 2 통과

으허헣ㅎㅎ.. 루트 노드가 삭제가 안되는 경우 빼고는 데이터 소실 없이 잘 작동한다.... 위에 있던 방어코드 없이도 무한루프에 빠지지 않는다. (해냈다 해냈어)<br/>
루트 노드 삭제가 가능할까 생각이 들긴 하지만 원리는 같을 것이라 생각한다.<br/>


## 나의 코드

상기했듯이 알고리즘 강의를 들으면서 코드를 따라 작성해봤고, 그 강의도 [이 링크를 참조하였다고 한다.](https://ejklike.github.io/2018/01/09/traversing-a-binary-tree-1.html)

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
        	        return
            elif value > self.current_node.value:
        	    if self.current_node.right != None:
        	        self.current_node = self.current_node.right
        	    else: # 빈 곳에 데이터 삽입
        	        self.current_node.right = Node(value)
        	        return
            elif value == self.current_node.value:
                print('데이터가 이미 있음.')
                return

# 이진 트리 탐색하기
    def search(self, value):
        self.current_node = self.head
        self.parent = self.current_node
        # 해당 값을 가지는 노드를 찾기
        while self.current_node:
            if self.current_node.value == value:
                return True
            elif value < self.current_node.value:
                self.parent = self.current_node
                self.current_node = self.current_node.left
            else:
                self.parent = self.current_node
                self.current_node = self.current_node.right
            

        return False

# 데이터 삭제 코드

    def delete(self, value):
        if not self.search(value):
            return

# 이후부터 Case를 분리해서 삭제코드 작성.

# Case 1 : 대상 노드가 Leaf Node일 때.
        if self.current_node.left == None and self.current_node.right == None:
            if value < self.parent.value:
                self.parent.left = None # 부모의 자식노드가 삭제되는 원리.
            else:
                self.parent.right = None

# Case 2 : 대상 노드가 자식노드가 1개일 때
        elif self.current_node.left != None and self.current_node.right == None: # 그럼 오른쪽 노드가 없는경우.
            if value < self.parent.value:
                self.parent.left = self.current_node.left
            else:
                self.parent.right = self.current_node.left
        elif self.current_node.right != None and self.current_node.left == None: # 그럼 왼쪽 노드가 없는경우.
            if value < self.parent.value:
                self.parent.left = self.current_node.right
            else:
                self.parent.right = self.current_node.right

# Case 3 : 대상 노드가 자식 노드가 2개일 때
# 1. 삭제할 Node의 오른쪽 자식 중, 제일 작은 값을 삭제할 Node의 부모노드가 가리키도록 한다.

        elif self.current_node.left != None and self.current_node.right != None:
            if value < self.parent.value:
                self.change_node = self.current_node.right
                self.change_node_parent = self.current_node.right

                # 순회과정
                while self.change_node.left != None:
                    self.change_node_parent = self.change_node
                    self.change_node = self.change_node.left

        	    # 순회 후 branch를 조절.
                if self.change_node_parent != self.change_node: # while 조건문 영향으로 순회가 바로 끝나버리는 경우
                    if self.change_node.right != None:
        	            self.change_node_parent.left = self.change_node.right
                    else:
        	            self.change_node_parent.left = None
                    
                    self.parent.left = self.change_node
                    self.change_node.right = self.current_node.right
                    self.change_node.left = self.current_node.left
                
                else:
                    if self.change_node.right != None:
                        self.change_node_parent.left = self.change_node.right
                    else:
                        self.change_node_parent.left = None
                    
                    self.parent.left = self.change_node
                    self.change_node.left = self.current_node.left


# 2. 삭제할 Node의 왼쪽 자식 중, max를 삭제할 Node의 부모노드가 가리키도록 한다.

            elif value > self.parent.value:
                self.change_node = self.current_node.left
                self.change_node_parent = self.current_node.left
            	# 순회과정
                while self.change_node.right != None:
            	    self.change_node_parent = self.change_node
            	    self.change_node = self.change_node.right
    
                # 순회 후 branch를 조절하기.
                if self.change_node_parent != self.change_node: # while 조건문 영향으로 순회가 바로 끝나버리는 경우
                    if self.change_node.left != None:
                        self.change_node_parent.right = self.change_node.left
                    else:
                        self.change_node_parent.right = None
                    
                    self.parent.right = self.change_node
                    self.change_node.right = self.current_node.right
                    self.change_node.left = self.current_node.left
                
                else:
                    if self.change_node.left != None:
                        self.change_node_parent.right = self.change_node.left
                    else:
                        self.change_node_parent.right = None
                    
                    self.parent.right = self.change_node
                    self.change_node.right = self.current_node.right

        return
```

그래서 이진트리관련해서 작성한 전체코드는 위와 같다.<br/>

## 마치며<br/>

무한루프에도 빠져보고 생각지 못한 오류가 많이 발생했고 이거를 수정하는 과정에서 무려 5시간 이상을 잡아먹은 것 같다..<br/>
그리고 다른 분들 것도 참고해보니 재귀용법을 적극적으로 사용하시는데 난 그런 생각을 자꾸 못하는 것 같다.. 아직 DP랑 백트래킹에 대해서 자세히 공부하지 않았지만 재귀용법이 많이 쓰일텐데 자꾸 마음에 걸린다..<br/>

그리고 또 한가지, 위에 코드를 적으면서 마음에 걸렸던 것이지만 아직 중복코드가 많은 것 같다.<br/>
더 코딩해보면서 이거를 줄이는 요령도 익혀야겠다.