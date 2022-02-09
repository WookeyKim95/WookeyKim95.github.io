---
layout: post
title:  "[자료구조] 이진트리(Binary Tree) 2"
subtitle:   "개요"
date: 2022-02-03 09:49:11 +0900
tags: data_structure
categories: study
comments: true
related_posts:
    - /_posts/study/2022-02-02-data-binary_tree_1.md
    
---

## 이진트리의 데이터 삭제코드 구현<br/>

이진트리에서 데이터를 삭제할 때, 삭제하고자 하는 노드의 형태에 따라서 크게 3 가지의 경우를 분리할 수 있다.<br/>

1. 삭제하고자하는 노드가 Terminal 노드일 때
2. 삭제하고자하는 노드의 자식 수가 1개 일 때<br/>
(이때 다시 그 하나뿐인 자식이 왼쪽에 있는지, 오른쪽에 있는지로 분리)
3. 삭제하고자하는 노드의 자식 수가 2개 일 때<br/>

### 해당 값을 찾는 노드를 찾기<br/>

```
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
```

2편에서 다루었던 search함수의 코드를 재활용 할 수 있다.<br/>
만약에 찾았으면 search = True로 바뀌면서 다음 과정으로 넘어가고, 그렇지 않으면 False를 return 하면서 함수를 종료한다.<br/>

### 1. Terminal 노드<br/>

```
# Case 1 : 대상 노드가 Leaf Node일 때.
        if self.current_node.left == None and self.current_node.right == None:
            if value < self.parent.value:
    	        self.parent.left = None # 부모의 자식노드가 삭제되는 원리.
            else:
    	        self.parent.right = None
```
삭제하고자 하는 노드가 Terminal 노드이면 부모노드와의 연결을 끊으면 된다. 
즉, 삭제하고자하는 노드에 도착하면 그 노드의 부모노드의 자식노드를 None으로 설정하면 된다.<br/>
다만, 부모노드의 어느 쪽 자식노드인지 주의할 필요가 있다.<br/>

### 2. 자식 노드가 1개인 노드일 때<br/>

```
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
```
삭제하고자 하는 노드의 자식노드가 1개 이면 어느쪽에 자식노드가 있는지, 그리고 부모노드의 어느쪽 자식노드인지 알아야 한다.<br/>
왼쪽에 자식노드가 있다면 부모노드와 왼쪽 자식노드를 연결하면 되고, 오른쪽에 자식노드가 있다면 부모노드와 오른쪽 자식노드를 연결하면 된다.<br/>

### 3. 자식 노드가 2개인 노드일 때<br/>

```
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

        	    # 순회 후 branch를 조절하기.
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

여기서는 다시 두 가지로 나뉜다.<br/>

- 삭제하고자 하는 노드가 부모 노드의 왼쪽 자식 노드인 경우.
    + 삭제할 노드의 오른쪽 자식들 중 (바로 아래 Level뿐 아니라 가장 깊은 Level까지) 제일 작은 값을 가진 노드를 부모노드의 왼쪽 자식으로 연결한다.
- 삭제하고자 하는 노드가 부모 노드의 오른쪽 자식 노드인 경우.
    + 삭제할 노드의 왼쪽 자식들 중 (바로 아래 Level뿐 아니라 가장 깊은 Level까지) 제일 큰 값을 가진 노드를 부모노드의 오른쪽 자식으로 연결한다.

자신보다 작은 값은 왼쪽으로, 큰 값은 오른쪽으로 보낸다는 이진 트리 법칙을 깨지 않으면서 연결을 바꾸는 방법으로 위의 과정을 거칠 수 있다.<br/>

따라서, 현재 구현된 삭제코드는 아래와 같다.<br/>
```
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

        	    # 순회 후 branch를 조절하기.
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

하지만 삽입, 서치, 삭제 코드를 테스트 해본 결과, 삭제 이후 서치하는 과정에서 **무한루프에 빠지는 에러가 발생한다.** 원인이 무엇이고 에러를 해결하는 과정은 다음 편에 적도록 하겠다.<br/>

**3편에서 계속**