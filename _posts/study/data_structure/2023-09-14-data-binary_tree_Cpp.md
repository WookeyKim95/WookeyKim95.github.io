---
layout: post
title:  "[자료구조] 이진트리(Binary Tree) C++"
subtitle:
date: 2023-09-14 07:23:11 +0900
tags: data_structure
categories: study
comments: true
related_posts:
    
---

# [자료구조] 이진트리(Binary Tree) C++<br/>

## 이진트리(Binary Tree)란?<br/>

![이진트리](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2022-02-02_binary_tree_1.jpg?raw=true)
<br/>

트리는 자료구조의 일종으로, 노드(Node)와 브랜치(Branch)를 사용해서 그래프가 사이클을 이루지 않도록 배치한 구조이다.<br/>

이진트리는 트리의 일종으로, 배열과 비슷한 기능을 하면서 배열보다 빠른 검색을 사용하기 위해서 사용하는 구조이다.<br/>
그래서 검색이나 탐색을 위한 알고리즘 구현을 위해서 많이 사용된다.<br/>

자식노드가 부모노드보다 값이 작으면 부모노드의 왼쪽에다가, 크면 부모노드의 오른쪽에다가 자식을 삽입한다.<br/>

## 이진트리의 종류<br/>

- 정이진 트리 : 자식노드가 0 또는 두개인 이진 트리를 말한다.
- 완전 이진 트리 : 왼쪽부터 채워진 이진트리를 말한다.
- 편향 이진 트리 : 자식노드가 한쪽으로만 쏠린 이진트리를 말한다.
- 포화 이진 트리 : 모든 노드가 꽉 찬 이진 트리를 말한다.
- 균형 이진 트리 : 왼쪽과 오른쪽 노드의 높이 차이가 1 이하인 이진트리 (레드블랙트리, AVL 트리 등)

![이진트리종류](https://github.com/wookikim95/wookikim95.github.io/blob/main/assets/img/study/algorithm/2023-09-14-data-binary_tree_Cpp_1.jpg?raw=true)

<br/>

## AVL트리와 레드블랙 트리<br/>

균형 이진 트리의 종류들이며, 삽입, 삭제, 탐색 모두 시간복잡도가 O(logN)이다.<br/>
<Br/>

### AVL 트리<br/>

두 자식 서브트리 높이는 항상 최대 1만큼 차이나는 특징을 가진 이진트리이다.<br/>

삽입, 삭제를 할 때 마다 균형이 안 맞는 부분을 맞추고자 트리 일부를 왼쪽 혹은 오른쪽으로 회전시키며 균형을 맞춘다.<br/>

### 레드블랙 트리<br/>

빨간색, 검은색의 색상을 나타내는 비트 정보를 추가로 저장한다.<br/>

모든 리프노드와 루트 노드는 블랙이고, 어떤 노드가 레드이면 그 노드의 자식은 반드시 블랙이라는 특징을 가진 이진트리이다.<br/>

## 이진트리의 C++ 코드 구현<br/>

```
#pragma once

#include <assert.h>


template <typename KEY, typename VALUE>
class CBinaryTreeNode
{
	template <typename KEY, typename VALUE>
	friend class CBinaryTree;

	template <typename KEY, typename VALUE>
	friend class CBinaryTreeIterator;

private:
	CBinaryTreeNode()	:
		m_Parent(nullptr),
		m_Left(nullptr),
		m_Right(nullptr),
		m_Next(nullptr),
		m_Prev(nullptr)
	{
	}

	~CBinaryTreeNode()
	{
	}

private:
	// 부모노드
	CBinaryTreeNode<KEY, VALUE>* m_Parent;
	// 왼쪽 자식노드
	CBinaryTreeNode<KEY, VALUE>* m_Left;
	// 오른쪽 자식노드
	CBinaryTreeNode<KEY, VALUE>* m_Right;
	// 다음노드
	CBinaryTreeNode<KEY, VALUE>* m_Next;
	// 이전노드
	CBinaryTreeNode<KEY, VALUE>* m_Prev;

public:
	KEY		first;
	VALUE	second;
};


template <typename KEY, typename VALUE>
class CBinaryTreeIterator
{
	template <typename KEY, typename VALUE>
	friend class CBinaryTree;

public:
	CBinaryTreeIterator()	:
		m_Node(nullptr)
	{
	}

	~CBinaryTreeIterator()
	{
	}

private:
	CBinaryTreeNode<KEY, VALUE>* m_Node;

public:
	// 연산자 재정의 : m_Node와 iter가 가리키는 노드가 같은가?
	bool operator == (const CBinaryTreeIterator<KEY, VALUE>& iter)	const
	{
		return m_Node == iter.m_Node;
	}
	// 연산자 재정의 : m_Node와 iter가 가리키는 노드가 다른가?
	bool operator != (const CBinaryTreeIterator<KEY, VALUE>& iter)	const
	{
		return m_Node != iter.m_Node;
	}

	// 연산자 재정의 : 다음 노드로 이동
	void operator ++ ()
	{
		m_Node = m_Node->m_Next;
	}

	// 연산자 재정의 : 다음 노드로 이동
	void operator ++ (int)
	{
		m_Node = m_Node->m_Next;
	}

	// 연산자 재정의 : 이전 노드로 이동
	void operator -- ()
	{
		m_Node = m_Node->m_Prev;
	}

	// 연산자 재정의 : 이전 노드로 이동
	void operator -- (int)
	{
		m_Node = m_Node->m_Prev;
	}

	// 클래스를 위한 연산자 재정의 : 포인터가 노드를 가리킴.
	CBinaryTreeNode<KEY, VALUE>* operator -> ()
	{
		return m_Node;
	}

	// 클래스를 위한 연산자 재정의 : 포인터가 노드를 가리키도록함.
	const CBinaryTreeNode<KEY, VALUE>* operator -> ()	const
	{
		return m_Node;
	}

	// 클래스를 위한 연산자 재정의 : 포인터
	CBinaryTreeNode<KEY, VALUE>* operator * ()
	{
		return m_Node;
	}

	// 클래스를 위한 연산자 재정의 : 포인터
	CBinaryTreeNode<KEY, VALUE>* operator * ()	const
	{
		return m_Node;
	}
};


// KEY : 탐색을 위한 타입
// VALUE : 실제 저장할 데이터를 위한 타입
template <typename KEY, typename VALUE>
class CBinaryTree
{
public:
	// 이진트리 클래스 생성자
	CBinaryTree()
	{
		// 새로운 노드 동적할당
		m_Begin = new NODE;
		m_End = new NODE;

		// 비긴노드와 엔드노드 연경
		m_Begin->m_Next = m_End;
		m_End->m_Prev = m_Begin;

		// 루트는 널포인터.
		m_Root = nullptr;
		m_Size = 0;
	}

	~CBinaryTree()
	{
		// 소멸자 - 동적할당됐던 모든 노드를 삭제하는 과정.
		// Begin부터 시작해서 다음 노드로 이동하면서 모두 삭제.
		PNODE	Node = m_Begin;

		while (Node)
		{
			PNODE	Next = Node->m_Next;

			delete	Node;

			Node = Next;
		}
	}

private:
	typedef CBinaryTreeNode<KEY, VALUE>		NODE;
	typedef CBinaryTreeNode<KEY, VALUE>*	PNODE;

public:
	typedef CBinaryTreeIterator<KEY, VALUE>	iterator;

private:
	// 루트노드, 비긴노드, 엔드노드 지정 (링크드리스트처럼?)
	PNODE		m_Root;
	PNODE		m_Begin;
	PNODE		m_End;
	int			m_Size;

public:
	// key에는 탐색을 위한 값, Data에는 실제 저장하고자 하는 값이 들어온다.
	void insert(const KEY& key, const VALUE& Data)
	{
		// 루트가 비어있을 경우 루트에다가 새로 노드를 형성함.
		if (!m_Root)
		{
			m_Root = new NODE;

			m_Root->first = key;
			m_Root->second = Data;

			m_Begin->m_Next = m_Root;
			m_Root->m_Prev = m_Begin;

			m_Root->m_Next = m_End;
			m_End->m_Prev = m_Root;
		}

		else
		{
			insert(key, Data, m_Root);
		}

		++m_Size;
	}

	int size()	const
	{
		return m_Size;
	}

	bool empty()	const
	{
		return m_Size == 0;
	}

	void clear()
	{
		//begin 노드와 end노드를 제외하고 모두 지워주는 과정
		PNODE	Node = m_Begin->m_Next;

		while (Node != m_End)
		{
			PNODE	Next = Node->m_Next;
			delete	Node;
			Node = Next;
		}

		m_Begin->m_Next = m_End;
		m_End->m_Prev = m_Begin;

		m_Size = 0;

		m_Root = nullptr;
	}

	iterator begin()	const
	{
		// begin노드 다음 지점을 반환하는 함수 (즉, 시작점)
		iterator	iter;
		iter.m_Node = m_Begin->m_Next;
		return iter;
	}

	iterator end()	const
	{
		// end노드를 반환하는 함수.
		iterator	iter;
		iter.m_Node = m_End;
		return iter;
	}

	iterator find(const KEY& key)
	{
		return find(key, m_Root);
	}

	// 지워질 경우 지운 다음노드를 리턴한다.
	// 지워질 데이터를 못찾았다면 end를 리턴한다.
	iterator erase(const KEY& key)
	{
		iterator	iter = find(key);

		if (iter == end())
			return end();

		return erase(iter);
	}

	iterator erase(const iterator& iter)
	{
		// 인자로 들어온 iterator가 가지고 있는 노드의 왼쪽에서 가장 큰
		// 노드를 얻어오거나 오른쪽에서 가장 작은 노드를 얻어와야 한다.
		// 만약 리프노드라면 그냥 지우면 된다.
		// 리프노드일 경우
		if (!iter.m_Node->m_Left && !iter.m_Node->m_Right)
		{
			// 부모노드가 있을 경우와 없을 경우로 판단한다.
			if (!iter.m_Node->m_Parent)
				m_Root = nullptr;

			else
			{
				// 지울 노드가 부모로부터 왼쪽 자식인지 오른쪽 자식인지를
				// 판단하여 그 부분을 자식이 없는것으로 만들어준다.
				if (iter.m_Node->m_Parent->m_Left == iter.m_Node)
					iter.m_Node->m_Parent->m_Left = nullptr;

				else
					iter.m_Node->m_Parent->m_Right = nullptr;
			}

			PNODE	Prev = iter.m_Node->m_Prev;
			PNODE	Next = iter.m_Node->m_Next;

			Prev->m_Next = Next;
			Next->m_Prev = Prev;

			delete	iter.m_Node;

			--m_Size;

			iterator	result;
			result.m_Node = Next;
			return result;
		}

		// 리프노드가 아니고 왼쪽 노드가 있을 경우 왼쪽노드의 자식노드중에서
		// 가장 큰 노드를 찾아온다.
		else if (iter.m_Node->m_Left)
		{
			PNODE	DeleteNode = FindMax(iter.m_Node->m_Left);

			// 노드의 key, value를 찾아준 노드로 변경해준다.
			iter.m_Node->first = DeleteNode->first;
			iter.m_Node->second = DeleteNode->second;

			// 지울 노드가 부모노드의 왼쪽 자식노드인지 오른쪽 자식
			// 노드인지를 판단한다.
			if (DeleteNode->m_Parent->m_Left == DeleteNode)
				DeleteNode->m_Parent->m_Left = DeleteNode->m_Left;

			else
				DeleteNode->m_Parent->m_Right = DeleteNode->m_Left;

			// 지울 노드의 왼쪽 자식노드가 있을 경우 해당 자식노드를
			// 지울노드의 자리로 대체시켜준다.
			if (DeleteNode->m_Left)
				DeleteNode->m_Left->m_Parent = DeleteNode->m_Parent;

			PNODE	Prev = DeleteNode->m_Prev;
			PNODE	Next = DeleteNode->m_Next;

			Prev->m_Next = Next;
			Next->m_Prev = Prev;

			--m_Size;

			delete	DeleteNode;

			iterator	result;
			result.m_Node = iter.m_Node->m_Next;
			return iter;
		}

		// 오른쪽 자식노드중에서 가장 작은 노드를 찾아와서 처리한다.
		PNODE	DeleteNode = FindMin(iter.m_Node->m_Right);

		// 노드의 key, value를 찾아준 노드로 변경해준다.
		iter.m_Node->first = DeleteNode->first;
		iter.m_Node->second = DeleteNode->second;

		// 지울 노드가 부모노드의 왼쪽 자식노드인지 오른쪽 자식
		// 노드인지를 판단한다.
		if (DeleteNode->m_Parent->m_Left == DeleteNode)
			DeleteNode->m_Parent->m_Left = DeleteNode->m_Right;

		else
			DeleteNode->m_Parent->m_Right = DeleteNode->m_Right;
		
		if (DeleteNode->m_Right)
			DeleteNode->m_Right->m_Parent = DeleteNode->m_Parent;

		PNODE	Prev = DeleteNode->m_Prev;
		PNODE	Next = DeleteNode->m_Next;

		Prev->m_Next = Next;
		Next->m_Prev = Prev;

		--m_Size;

		delete	DeleteNode;

		iterator	result;
		result.m_Node = iter.m_Node->m_Next;
		return iter;
	}

	/*
	전위순회 : 부모 -> 왼쪽 -> 오른쪽
	중위순회 : 왼쪽 -> 부모 -> 오른쪽
	후위순회 : 왼쪽 -> 오른쪽 -> 부모
	부모가 노드방문한 것이다.
	*/
	void PreOrder(void(*Func)(const iterator&, const iterator&,
		const iterator&, const iterator&))
	{
		PreOrder(Func, m_Root);
	}

	void InOrder(void(*Func)(const iterator&, const iterator&,
		const iterator&, const iterator&))
	{
		InOrder(Func, m_Root);
	}

	void PostOrder(void(*Func)(const iterator&, const iterator&,
		const iterator&, const iterator&))
	{
		PostOrder(Func, m_Root);
	}

private:
	void insert(const KEY& key, const VALUE& Data, PNODE Node)
	{
		// 노드의 키보다 추가할 키가 더 작을 경우 왼쪽으로 보낸다.
		if (key < Node->first)
		{
			// 왼쪽 노드가 없을 경우에는 왼쪽 노드에 추가하고 끝낸다.
			if (!Node->m_Left)
			{
				PNODE	NewNode = new NODE;

				NewNode->first = key;
				NewNode->second = Data;

				Node->m_Left = NewNode;
				NewNode->m_Parent = Node;

				/*
				왼쪽으로 추가되는 노드는 다음노드로 자신의 부모노드가 되고
				이전 노드는 부모노드의 이전노드를 지정하면 된다.
				*/
				PNODE	Prev = Node->m_Prev;

				Prev->m_Next = NewNode;
				NewNode->m_Prev = Prev;

				NewNode->m_Next = Node;
				Node->m_Prev = NewNode;
				return;
			}

			// 왼쪽노드가 있다면 왼쪽 노드를 넣어주면서 다시 탐색을 하도록 한다.
			insert(key, Data, Node->m_Left);
		}

		// 노드의 키보다 추가할 키가 더 작을 경우 오른쪽으로 보낸다.
		else
		{
			// 오른쪽 노드가 없을 경우 여기에 추가한다.
			if (!Node->m_Right)
			{
				PNODE	NewNode = new NODE;

				NewNode->first = key;
				NewNode->second = Data;

				// 현재 노드의 오른쪽 자식노드로 지정한다.
				Node->m_Right = NewNode;

				// 새로 추가되는 노드의 부모를 현재 노드로 지정한다.
				NewNode->m_Parent = Node;

				/*
				오른쪽으로 추가될때는 이전노드를 부모노드로 지정하고
				다음노드를 부모노드의 다음노드로 지정한다.
				*/
				PNODE	Next = Node->m_Next;

				// 부모노드와 새로 생성된 노드를 연결한다.
				Node->m_Next = NewNode;
				NewNode->m_Prev = Node;

				NewNode->m_Next = Next;
				Next->m_Prev = NewNode;
				return;
			}

			insert(key, Data, Node->m_Right);
		}
	}

	iterator find(const KEY& key, PNODE Node)
	{
		// 더이상 노드가 없다면 못찾았다는 의미이므로 end를 리턴한다.
		if (!Node)
			return end();

		// 찾았을 경우 iterator를 만들어서 리턴한다.
		if (key == Node->first)
		{
			iterator	iter;
			iter.m_Node = Node;
			return iter;
		}

		// 더 작을 경우 왼쪽으로 탐색해서 들어간다.
		else if (key < Node->first)
			return find(key, Node->m_Left);

		// 그게 아닐 경우라면 크다는 의미이므로 오른쪽으로 탐색해서 들어간다.
		return find(key, Node->m_Right);
	}

	PNODE FindMax(PNODE Node)	const
	{
		if (!Node->m_Right)
			return Node;

		return FindMax(Node->m_Right);
	}

	PNODE FindMin(PNODE Node)	const
	{
		if (!Node->m_Left)
			return Node;

		return FindMin(Node->m_Left);
	}

	// 전위순회 : 부모 -> 왼쪽 -> 오른쪽
	void PreOrder(void(*Func)(const iterator&, const iterator&,
		const iterator&, const iterator&), PNODE Node)
	{
		if (!Node)
			return;

		// 노드 데이터를 출력한다.
		iterator	iter, iterParent, iterLeft, iterRight;
		iter.m_Node = Node;
		iterParent.m_Node = Node->m_Parent;
		iterLeft.m_Node = Node->m_Left;
		iterRight.m_Node = Node->m_Right;

		Func(iter, iterParent, iterLeft, iterRight);

		PreOrder(Func, Node->m_Left);
		PreOrder(Func, Node->m_Right);
	}

	// 중위순회: 왼쪽->부모->오른쪽
	void InOrder(void(*Func)(const iterator&, const iterator&,
		const iterator&, const iterator&), PNODE Node)
	{
		if (!Node)
			return;

		// 노드 데이터를 출력한다.
		iterator	iter, iterParent, iterLeft, iterRight;
		iter.m_Node = Node;
		iterParent.m_Node = Node->m_Parent;
		iterLeft.m_Node = Node->m_Left;
		iterRight.m_Node = Node->m_Right;

		InOrder(Func, Node->m_Left);

		Func(iter, iterParent, iterLeft, iterRight);

		InOrder(Func, Node->m_Right);
	}

	// 후위순회 : 왼쪽->오른쪽->부모
	void PostOrder(void(*Func)(const iterator&, const iterator&,
		const iterator&, const iterator&), PNODE Node)
	{
		if (!Node)
			return;

		// 노드 데이터를 출력한다.
		iterator	iter, iterParent, iterLeft, iterRight;

		iter.m_Node = Node;
		iterParent.m_Node = Node->m_Parent;
		iterLeft.m_Node = Node->m_Left;
		iterRight.m_Node = Node->m_Right;

		PostOrder(Func, Node->m_Left);

		PostOrder(Func, Node->m_Right);

		Func(iter, iterParent, iterLeft, iterRight);
	}
};
```