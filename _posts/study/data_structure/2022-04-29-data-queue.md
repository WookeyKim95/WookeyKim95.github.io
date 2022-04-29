---
layout: post
title:  "[자료구조] 큐(Queue)를 C로 구현하기"
subtitle:   "개요"
date: 2022-04-29 09:21:28 +0900
tags: data_structure
categories: study
comments: true
related_posts:

---

# [자료구조] 큐(Queue)를 C로 구현하기<br/>

학원에서 배운 C로 Queue을 구현하는 과정을 이 포스트에다가 저장한다.<br/>
<br/>

## 전체 소스코드<br/>
<br/>

```
i#include <stdio.h>
#include <stdlib.h>

#pragma warning (disable : 4996)

// 원형 큐

typedef struct queue {
    int* arr;
    int front; // 데이터를 꺼내는 쪽
    int rear; // 데이터를 넣는 쪽
    int count; // 데이터가 몇개 들어있는지 저장할 변수
    int capacity; // 배열의 최대용량
}queue;

void queue_init(queue* que, int size);
void enqueue(queue* que);
void dequeue(queue* que);
void print(queue* que);

int main() {
    queue que; // 구조체 변수 선언
    int size, choice;

    printf("\n\n 큐의 크기를 입력하세요 : ");
    scanf("%d", &size);
    
    queue_init(&que, size);

    while(1) {
        system("clear");
        printf("\t\t**** queue의 메뉴 ****\n\n");

        printf("1. enqueue   2. dequeue   3. print   4. clear   0. terminate\n");
        printf("번호를 입력하세요 : ");
        scanf("%d", &choice);
        getchar();

        switch (choice)
        {
        case 1:
            enqueue(&que);
            break;
        case 2:
            dequeue(&que);
            break;
        case 3:
            print(&que);
            break;
        case 4:
            que.front = que.rear = que.count = 0;
            break;
        case 0:
            free(que.arr);
            exit(0);
        }

    }
    return 0;
}

void queue_init(queue* que, int size) {
    que->arr = (int*)malloc(sizeof(int) * size);
    que->front = que->rear = que->count = 0;
    que->capacity = size;
}

void enqueue(queue* que) {
    int _input;
    printf("삽입할 값을 입력하세요 : ");
    scanf("%d", &_input);
    getchar();

    if (que->count >= que->capacity) {
        printf("Overflow가 발생합니다.\n");
        return;
    }
    
    que->arr[que->rear] = _input;
    que->rear++;
    que->count++;

    if (que->rear >= que->capacity) {
        que->rear = 0;
    }
    
    return;
}

void dequeue(queue* que) {
    printf("%d\n", que->arr[que->front]);

    if (que->count <= 0) {
        printf("underflow가 발생합니다.\n");
        return;
    }

    que->front++;
    que->count--;

    if (que->front >= que->capacity) {
        que->front = 0;
    }
    printf("pop 완료.");
    getchar();

    return;
}

void print(queue* que) {
    
    if (que->count == 0) {
        printf("출력할 데이터가 없습니다.\n");
    }

    int i = que->front;

    while (1) {
        printf("%d ", que->arr[(i % que->capacity)]);
        i++;
        if (i % que->capacity == que->rear) {
            break;
            }
        }
    
    /*
    for (i = 0; i < que->count; i++) //count개 출력
	{
		printf("%d ", que->arr[(que->front + i) % que->capacity]);
        // ########### 배열의 회전출력 참고. 인덱스의 범위를 %로 #############
	}
    */
	
    getchar();

    return;
}
```

원형 큐로 구현되었으며, front와 rear 포인터를 지정한 후, rear에 값을 삽입하면 rear가 그 다음으로 이동하고,<br/>
마찬가지로 front에서 값을 pop하면 front가 다음으로 이동하는 구조이다.<br/>
<br/>

그리고 원형 큐는 순회할 때 %(나머지 구하기)를 이용할 수 있다.<br/>

맨 아래 print 코드를 보면 현재 배열의 count만큼 반복하는 구문이있는데,<br/>
capacity에 대해서 %를 진행하면<br/>

예를 들어 capacity가 5이면, <br/>

- 4번째 값을 볼 때에는 3 % 5 = 3, 즉 3번인덱스를 print.
- 2번째 값을 볼 때에는 1 % 5 = 1을 하거나 6 % 5 = 1을 이용하여 1번 인덱스를 print.

%를 유용하게 씀으로써 원형 큐를 순회할 수 있다.<br/>