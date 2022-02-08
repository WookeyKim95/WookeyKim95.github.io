---
layout: post
title:  "[자료구조] 링크드리스트(Linked_List) 4"
subtitle:   "개요"
date: 2022-01-26 19:44:20 +0900
tags: data_structure
categories: study
comments: true
related_posts:

---

## 이중 링크드리스트<br/>
<br/>
지난 3편 까지 코드에는 다음 노드의 주소에 대한 정보만을 저장했는데, 이전 노드의 주소에 대한 정보도 저장하면 더 좋을 것 같다는 생각이 든다.<br/>
여기서 이중 링크드리스트라는 것을 발견하였다.<br/>
<br/>
그리고 리스트를 관리하기 용이하게 코드를 짜면 더 좋을 것 같다는 생각도 들었다.<br/>
<br/>
그러면 코드는 어떻게 짤 수 있을까?<br/>

### 1. 이전 노드의 주소에 대한 정보도 추가하자.<br/>
```
class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next
```
다음 노드의 주소에 대한 정보를 추가한 것 처럼, 이전 노드의 주소에 대한 정보, `self.prev`를 새로 추가해준다.<br/>
<br/>

### 2. 링크드리스트 내의 노드 관리를 위한 Class를 새로 추가하자.<br/>
```
class Node_Manage:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head
```
링크드리스트를 새로 만든다면, 시점(head)와 종점(tail)은 같은 노드일 것이다.<br/>
따라서, self.head는 새로운 노드 Node(data)로 지정함과 동시에, self.tail도 self.head와 같은 노드로 지정해준다.<br/>
<br/>

### 3. 이중 링크드리스트 맨 뒤에 노드를 추가하는 과정.<br/>
```
def add(self, data): # 이중 링크드리스트의 데이터 추가
    if self.head == None: # 데이터가 모두 삭제되어 리스트가 비어있을 때 작동.
        self.head = Node(data)
        self.tail = self.head
    else:
        node = self.head
        while node.next:
            node = node.next
                
        new = Node(data)
        node.next = new
        new.prev = node
        self.tail = new
        
        return
```
다음은 이중 링크드리스트 맨 뒤에 데이터를 추가하는 과정이다.<br/>
혹시나, 링크드리스트에 head가 없는 경우는 데이터가 모두 삭제되어 이중 링크드리스트가 비어있는 경우이므로, 새로운 리스트를 만드는 것 처럼 코드를 실행한다.<br/>
<br/>
대부분은 리스트에 데이터가 있을 테니 else 구문이 작동할 것이다.<br/>
1~3편 까지의 내용과 마찬가지로 리스트를 끝까지 탐색한 뒤에 맨 뒤에 새로운 노드를 추가해준다.<br/>
다만 차이점이라면 이중 링크드리스트의 head는 변함이 없지만, tail은 새로 추가된 노드로 변하므로,<br/>

```
new.prev = node
# 새로 추가된 노드의 이전 노드 주소 정보는 원래 맨끝의 노드의 주소로 지정,

self.tail = new
# 이중 링크드리스트의 종점은 새로 추가된 노드로 지정해준다.
```
<br/>

### 4. 이중 링크드리스트의 특정 지점에 새로운 데이터 추가하기.<br/>

```
def insert(self, data, new_data):
    node = self.head
    
    try:
        while node.data != data:
            node = node.next
        
    except AttributeError:
        print('찾는 내용이 없습니다.')
        return
    
    if node == self.tail:
        node.next = Node(new_data)
        node.next.prev = node
        self.tail = node.next
        return
    else:
        tem = node.next 
        node_new = Node(new_data) 
        node.next = node_new
        node_new.prev = node
        node = node_new
        
        # node = node_new로 새로 추가된 노드로 이동했으니까 이동한 노드에 대한 작업!
        # 변수 헷갈림 주의!
        node.next = tem
        node.next.prev = node
        
        return
```
일반 링크드리스트일때와 큰 차이는 없다!<br/>
다만 추가된 내용은 새로운 노드의 prev를 새로 지정해주어야 한다는 점으로, 조금 더 코드가 복잡해진다.<br/>
<br/>
위의 코드는 특정 데이터 다음 지점에 노드를 추가했는데, 특정 데이터 이전 지점에 노드를 추가하도록 코드를 짤 수 있다!<br/>

```
def insert_prev(self, data, new_data):
    node = self.tail
    
    try:
        while node.data != data:
            node = node.prev
        
    except AttributeError:
        print('찾는 내용이 없습니다.')
        return
    
    if node == self.tail:
        node.prev = Node(new_data)
        node.prev.next = node
        self.tail = node.prev
        return
    
    else:
        tem = node.prev
        node_new = Node(new_data) 
        node.prev = node_new
        node_new.next = node
        node = node_new
        
        node.prev = tem
        node.prev.next = node
        
        return
```
이렇게 next와 prev를 서로 바꿔주고, head를 tail로 잘 바꿔주면 된다!<br/>

### 5. 이중 링크드리스트의 특정 데이터 삭제하기.<br/>

```
def delete(self, data):
        
    if data == self.head.data:
        node = self.head
        self.head = self.head.next
        del node
        return
    
    node = self.head
    
    try:
        while (node.next).data != data:
            node = node.next
        
    except AttributeError:
        print('찾는 내용이 없습니다.')
        return
    
    temp = (node.next).next
    
    del node.next

    node.next = temp
    node.next.prev = node
    
    return
```
마찬가지로 삭제하는 코드도 같은 원리로 적용할 수 있다!<br/>
<br/>

### 6. 탐색<br/>
이중 링크드리스트의 장점은 정방향 탐색도 가능하고, 역방향 탐색도 가능하다는 점이다.<br/>
```
def node_print_head(self):
    node = self.head
    while node:
        print(node.data)
        node = node.next
        
    return
```
정방향 탐색은 일반 링크드리스트가 하는 것처럼 head부터 탐색하도록코드를 짜면 된다!<br/>
<br/>
한편 역방향은 tail부터 시작할 것이다.<br/>

```
def node_print_tail(self):
    node = self.tail
    while node:
        print(node.data)
        node = node.prev
        
    return
```

따라서, tail부터 시작해서 prev 방향으로 탐색하도록 코드를 짜면 된다! <br/>

## 테스트

종합하면 작성된 코드는 다음과 같다.

```
class Node:
    def __init__(self, data, prev=None, next=None):
        self.data = data
        self.prev = prev
        self.next = next

class Node_Manage:
    def __init__(self, data):
        self.head = Node(data)
        self.tail = self.head
    
    def add(self, data): # 이중 링크드리스트의 데이터 추가
        if self.head == None:
            self.head = Node(data)
            self.tail = self.head
        else:
            node = self.head
            while node.next:
                node = node.next
                
            new = Node(data)
            node.next = new
            new.prev = node
            self.tail = new
            
            return

    def node_print_head(self):
        node = self.head
        while node:
            print(node.data)
            node = node.next
            
        return
    
    def node_print_tail(self):
        node = self.tail
        while node:
            print(node.data)
            node = node.prev
            
        return


    def insert(self, data, new_data):
        node = self.head
        
        try:
            while node.data != data:
                node = node.next
            
        except AttributeError:
            print('찾는 내용이 없습니다.')
            return
        
        if node == self.tail:
            node.next = Node(new_data)
            node.next.prev = node
            self.tail = node.next
            return
        else:
            tem = node.next 
            node_new = Node(new_data) 
            node.next = node_new
            node_new.prev = node
            node = node_new
            
            node.next = tem
            node.next.prev = node
            
            return
        
    def insert_prev(self, data, new_data):
        node = self.tail
        
        try:
            while node.data != data:
                node = node.prev
            
        except AttributeError:
            print('찾는 내용이 없습니다.')
            return
        
        if node == self.tail:
            node.prev = Node(new_data)
            node.prev.next = node
            self.tail = node.prev
            return
        
        else:
            tem = node.prev
            node_new = Node(new_data) 
            node.prev = node_new
            node_new.next = node
            node = node_new
            
            node.prev = tem
            node.prev.next = node
            
            return

    def delete(self, data):
        
        if data == self.head.data:
            node = self.head
            self.head = self.head.next
            del node
            return
        
        elif data == self.tail.data:
            node = self.tail
            self.tail = self.tail.prev
            del node
            return
        
        node = self.head
        
        try:
            while (node.next).data != data:
                node = node.next
            
        except AttributeError:
            print('찾는 내용이 없습니다.')
            return
        
        temp = (node.next).next
       
        del node.next

        node.next = temp
        node.next.prev = node
        
        return
```

이제 아래의 코드를 실행하여 출력결과를 살펴보자.<br/>

```
A = Node_Manage(0)
A.add(1)
A.add(2)

A.node_print_head()
print()
A.node_print_tail()
print()
A.insert(1, 3)
A.insert(3, 4)
A.insert(2, 5)
A.insert_prev(3, '라라')

A.node_print_head()
print()
A.node_print_tail()
print()

A.delete(4)
A.delete(4)
A.node_print_head()
print()
A.node_print_tail()
print()
```

Spyder에서 구동을 해보았다.<br/>

```
# 출력 결과
0
1
2

2
1
0

0
1
라라
3
4
2
5

5
2
4
3
라라
1
0

찾는 내용이 없습니다.
0
1
라라
3
2
5

5
2
3
라라
1
0
```
의도한대로 결과가 잘 출력되었다!<br/>

**5편에서 계속**