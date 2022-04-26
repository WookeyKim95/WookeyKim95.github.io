---
layout: post
title:  "[자료구조] Linked_List를 구현하기 (C언어)"
subtitle:   "개요"
date: 2022-04-26 09:37:50 +0900
tags: data_structure
categories: study
comments: true
related_posts:

---

# [자료구조] Linked_List를 구현하기 (C언어)<br/>

## 링크드리스트를 C로 구현해보자.<br/>
<br/>

구현 툴 : 리눅스 VSCode<br/>

학원에서 자료구조 알고리즘을 배우며 코딩했던 내용을 이곳에 기록한다.<br/>
<br/>

### 단일 링크드리스트 구현하기<br/>
<br/>

소스코드 저장

```
#include <stdio.h>
#include <stdlib.h> // malloc과 free 사용을 위한 것


# pragma warning (disable : 4996)

enum linkedListMenu {
    TERMINATE, INSERT_FRONT, INSERT_REAR, DISPLAY, REMOVE_FIRST, REMOVE_VALUE, COUNT, INSERT_Kth_F, INSERT_SORT_ASCEN, REVERSE
};

typedef struct Node {
    int val; // 값
    struct Node* next; // 다음 노드의 주소
}Node;

Node* head = NULL; // 첫 노드의 주소를 저장한다.(전역변수)
int count = 0; // 노드의 개수를 전역변수로 설정

void insertNodeFront();
void insertNodeRear();
void display();
void free_node();
void remove_first();
void remove_value();
void add_Node(Node* newNode, Node* curNode, Node* prevNode, int th);
void insert_kth();
void insert_ascen();
void reverse_sort();

int main() {
    int choice;


    while (1) {
        system("clear");
        printf("*** 단일 연결 리스트 메뉴 선택 ***\n\n");
        printf("1. 첫 자리에 노드 생성\n");
        printf("2. 마지막 자리에 노드 생성\n");
        printf("3. 노드 연결리스트 데이터 출력\n");
        printf("4. 첫 노드 삭제\n");
        printf("5. 입력 값 노드 삭제\n");
        printf("6. 노드의 개수 출력\n");
        printf("7. 특정위치 노드 삽입\n");
        printf("8. 노드 생성(정렬삽입)\n");
        printf("9. 노드 역순 정렬\n");
        printf("0. 노드 프로그램 종료\n");

        printf("\n메뉴 선택 : [ ]\b\b");
        scanf("%d", &choice);
        getchar();

        switch (choice)
        {
        case INSERT_FRONT:
            insertNodeFront(); // 맨 앞에 노드 삽입
            break;
        case INSERT_REAR:
            insertNodeRear();
            break;
        case DISPLAY:
            display();
            break;
        case REMOVE_FIRST:
            remove_first();
            break;
        case REMOVE_VALUE:
            remove_value();
            break;
        case COUNT:
            printf("현재 노드의 수는 %d개 입니다.\n", count);
            getchar();
            break;
        case INSERT_Kth_F:
            insert_kth();
            break;
        case INSERT_SORT_ASCEN:
            insert_ascen();
            break;
        case REVERSE:
            reverse_sort();
            break;
        case TERMINATE:
            free_node();
            printf("종료합니다.\n");
            exit(0); // 프로그램 강제 종료
        }

        printf("\n\n\t\t");
    }
    return 0;
}


// 맨 처음 노드를 추가하는 코드
void firstNode(Node* newNode) {
    head = newNode;
    count = 1;
    printf("연결리스트의 첫 노드가 설정되었습니다.\n");
    getchar();
    return;
}

// 맨 앞 삽입 시 데이터는 역순 저장된다.
void insertNodeFront() {

    Node* newNode;
    newNode = (Node*)malloc(sizeof(Node));

    printf("\n\n 정수입력 : ");
    scanf("%d", &newNode->val); // 포인터로 접근할 때에는 화살표
    newNode->next = NULL;
    getchar();

    if (head == NULL) { // 연결리스트가 구성되지 않은 상태.
        firstNode(newNode);
        return;
    }

    //1. 새로 생성된 논드와 이전 노드를 연결하는 과정
    newNode->next = head;
    head = newNode;
    count++;
    printf("삽입완료");
    getchar();
    return;
}

void insertNodeRear() { // 맨 뒤에 노드 삽입하기


    // 노드 생성 후 값 설정
    Node* newNode, *curNode = head;
    
    newNode = (Node*)malloc(sizeof(Node));

    printf("\n\n 정수입력 : ");
    scanf("%d", &newNode->val); // 포인터로 접근할 때에는 화살표
    newNode->next = NULL;
    getchar();

    if (head == NULL) {
        firstNode(newNode);
        return;
    }

    // 마지막 노드로 이동
    while(curNode->next != NULL) {
        curNode = curNode->next;
    }

    // 다음 노드가 없을 때 다음 노드에 새 노드를 삽입.
    curNode->next = newNode;
    count++;
    printf("삽입완료");
    getchar();
    return;
}

void display() { // 시간복잡도 : O(N)
    Node* curNode; // 순회하는 목적으로 사용하는 포인터
    curNode = head; //첫 노드는 항상 head

    // 연결리스트가 구성되지 않은 경우??
    if (head == NULL || count == 0) {
        printf("연결리스트가 아직 구성되지 않았습니다.\n");
        getchar();
        return;
    }

    while(curNode->next != NULL) {
        printf("%d => ", curNode->val);
        curNode = curNode->next;
    }

    printf("%d\n", curNode->val);

    getchar();
    return;
}

void remove_first() {

    Node* rm_node = head;

    if (count == 0) {
        printf("삭제할 노드가 없습니다.\n");
        return;
    }
    
    head = rm_node->next;
    free(rm_node);
    count--;
    printf("첫 노드 삭제가 완료되었습니다.\n");
    getchar();
    return;

}

void remove_value() {

    int data;
    Node* delNode, *curNode, *prevNode;

    if (head == NULL || count == 0) { // count : 노드의 개수 전역 변수
        printf("연결리스트가 구성되어있지 않습니다.\n");
        getchar();
        return;
    }

    printf("삭제하고자 하는 수 입력 : ");
    scanf("%d", &data);
    getchar(); // system("pause") 기능.

    curNode = head;
    prevNode->next = curNode;

    // 삭제할 데이터
    while (curNode != NULL) {

        // 순회 검증용 printf("%d %d\n", prevNode->val, curNode->val);

        if (curNode->val == data) { // segmentation fault 방지를 위한 if문.
            delNode = curNode;
            
            if (curNode == head) {
                head = head->next;
            }

            curNode = curNode->next; // 우선 curNode를 옮김
            prevNode->next = curNode; // 그 후 prevNode의 next를 재지정

            free(delNode); // 
            printf("%d 값 노드 제거 완료.\n", data);
            count--; // 노드의 개수를 전역변수로 설정한 상태.
        }
        else { // 값이 일치하지 않을경우 그냥 순회를 진행함.
            prevNode = curNode;
            curNode = curNode->next;
        }


    }

    // 이 곳에 왔다는 것은 curNode == NULL 이라는 의미.
    // 그리고 prevNode가 마지막 노드.

    if (prevNode->val == data) {

        free(prevNode);
        printf("%d 값 노드 제거 완료.\n", data);
        count--;// 노드의 개수를 전역변수로 설정한 상태.

        printf("수행 완료.\n");
        getchar();
        return;
    }

    printf("수행 완료.\n");
    getchar();
    return;

}

void free_node() {
    Node* delNode = head;
    // 첫 노드는 항상 head
    if (head == NULL) {
        return;
    }

    // delNode에 저장한 뒤에 head 부터 순차적으로 없애는 원리
    while (delNode->next != NULL) {
        head = head->next;
        printf("동적 malloc으로 생성된 %d노드 제거\n", delNode->val);
        free(delNode);
        delNode = head;
    }

    printf("동적 malloc으로 생성된 %d노드 제거\n", delNode->val);
    free(delNode);

}

void add_Node(Node* newNode, Node* curNode, Node* prevNode, int th) {
    
    // newNode의 val 값을 입력받는다.
    printf("%d번째에 새로운 값을 입력합니다. 값을 입력해주세요. : ", th);
    scanf("%d", &newNode->val);
    getchar();

    // prevNode와 curNode 사이에 newNode를 삽입하는 작업
    newNode->next = curNode;
    prevNode->next = newNode;
    count++;
}

void insert_kth() { // 시간복잡도 : O(N)
    Node* newNode, *prevNode, *curNode = head;
    int th, cnt = 1;
    printf("몇 번째 노드에 값을 넣으시겠어요? : ");
    scanf("%d", &th);
    getchar();

    if (th > count + 1) {
        printf("입력하려는 위치가 노드의 길이를 초과합니다.");
        getchar();
        return;
    }

    else if (th == 1) {
        // 첫번째 노드일 경우 맨 처음에 노드를 넣는 함수로 보냄.
        printf("1번째 노드에 새로운 값을 넣습니다.");
        insertNodeFront();
        /*
        newNode->next = head;
        head = newNode;
        */
        return;
    }

    else if (th == count + 1) {
        // 맨 마지막에 추가하고 싶을 경우 맨 마지막에 노드를 넣는 함수로 보냄.
        printf("%d번째 노드에 새로운 값을 넣습니다.", count + 1);
        insertNodeRear();
        return;
    }

    // 동적메모리 할당
    newNode = (Node*)malloc(sizeof(Node));
    prevNode = (Node*)malloc(sizeof(Node)); // 이전 노드를 저장해둘 포인터 변수

    prevNode = curNode;

    while (curNode->next != NULL) {

        if (cnt == th) {
            // 목표에 도달 했을 시 삽입작업.
            add_Node(newNode, curNode, prevNode, th);
            printf("%d번째에 새로 저장이 완료되었습니다.\n", cnt);
            getchar();
            return;
        }

        // 검즘용 printf("%d %d %d\n", prevNode->val, curNode->val, curNode->next->val);
        
        // prevNode를 curNode로 만들어 준 후 curNode를 다음 노드로 이동.
        prevNode = curNode; 
        curNode = curNode->next;
        cnt++;
    }

    // curNode->가 NULL인 상태이면 마지막 자리에 왔다는 이야기이다.
    // 이 상태에서는 마지막 직전자리에 값을 넣게 된다.
    // ex) 1 - 2 - 3 - 4 상태에서 4번째 자리에 새로 5를 넣을 수 있는 상태
    // 적용시 1 - 2 - 3 - 5 - 4
    add_Node(newNode, curNode, prevNode, th);

    printf("%d번째에 새로 저장이 완료되었습니다.\n", cnt);
    getchar();


    return;
}


void insert_ascen() {
    Node* newNode, *curNode, *prevNode;
    newNode = (Node*)malloc(sizeof(Node));

    printf("정수 입력 : ");
    scanf("%d", &newNode->val);
    newNode->next = NULL;
    getchar();

    // 1. 연결리스트가 구성되지 않은 경우
    if (head == NULL) {
        head = newNode;
        printf("연결리스트의 첫 노드 구성이 완료되었습니다.\n");
        count = 1;
        return;
    }

    // 2. 새 노드가 첫 노드보다 작은 경우는 첫 노드로 추가.
    if (newNode->val < head->val) {
        newNode->next = head;
        head = newNode;
        printf("가장 작은 값 입력으로 맨 앞 삽입\n");
        return;
    }

    // 3. 일반적인 경우
    curNode = prevNode = head;
    while (curNode->next != NULL) {
        curNode = curNode->next;

        if (curNode->val > newNode->val) {
            prevNode->next = newNode;
            newNode->next = curNode;
            printf("일반적인 상황 삽입완료.\n");
            getchar();
            return;
        }
        // 비교한 후에 이동을 시키기!
        prevNode = prevNode->next;
    }

    curNode->next = newNode;
    printf("가장 큰 값으로 맨 뒤에 삽입 완료.\n");
    getchar();
    return;

}

void reverse_sort() {

    if (count == 0 || count == 1) {
        return; // 노드 개수가 없거나 1개면 역순 정렬할 필요가 없음.
    }

    Node *curNode;
    Node *temp_prev, *temp_next, *tail;

    tail = head;
    
    curNode = head->next;
    temp_prev = head;

    while (curNode->next != NULL) {

        temp_next = curNode->next;
        curNode->next = temp_prev;

        temp_prev = curNode;
        curNode = temp_next;
        }

    // 마지막 노드 도달

    head = curNode;
    head->next = temp_prev;
    tail->next = NULL;

    printf("역순 연결이 완료되었습니다.\n");
    getchar();
    return;
}
```


### 이중 링크드리스트 구현하기<br/>
<br/>

소스코드 저장

```
#include <stdio.h>
#include <stdlib.h>

# pragma warning (disable : 4996)

enum linkedListMenu {
    TERMINATE, INSERT_FRONT, INSERT_REAR, INSERT_Kth_F, INSERT_SORT_ASCEN, DISPLAY, REMOVE_FIRST, REMOVE_VALUE, COUNT, N_VALUE, REVERSE
};

//----------------------------- enumset --------------------------------

typedef struct doubleNode {
    int val;
    struct doubleNode* next, *prev;
}DNode;

//----------------------------- 자료정의 --------------------------------

DNode* head = NULL; // 시작 노드의 주소를 저장하는 포인터
DNode* rear = NULL; // 마지막 노드의 주소를 저장하는 포인터
int count = 0;

//----------------------------- 젼역변수 --------------------------------
DNode* createNode();
void insertSort();
void display();
void insertFront();
void insertRear();
void remove_value();
void free_node();
void add_Node(DNode* newNode, DNode* curNode, DNode* prevNode, int th);
void insert_kth();
void remove_first();
void n_value();
void reverse_sort();

//---------------------------- 함수 선언부 ------------------------------
int main() {
    int choice;

    while (1) {
        system("clear");
        printf("#### 이중연결 리스트 메뉴 ####\n\n");
        printf("1. 맨 앞에 노드 추가\n");
        printf("2. 맨 뒤에 노드 추가\n");
        printf("3. 특정 위치에 노드 추가\n");
        printf("4. 정렬 노드 추가\n");
        printf("5. 연결 리스트 출력\n");
        printf("6. 첫 번째 노드 삭제\n");
        printf("7. 특정 값 노드 하나 삭제\n");
        printf("8. 노드의 개수 출력\n");
        printf("9. n번째 노드의 값 구하기\n");
        printf("10. 역순 연결\n");
        printf("0. 프로그램 종료\n");

        printf("\n메뉴 선택 : [ ]\b\b");
        scanf("%d", &choice);
        getchar();

        switch (choice)
        {
        case INSERT_FRONT:
            insertFront(); // 맨 앞에 노드 삽입
            count++;
            break;
        case INSERT_REAR:
            insertRear(); // 맨 뒤에 노드 삽입
            count++;
            break;
        case INSERT_Kth_F:
            insert_kth();
            count++;
            break;
        case INSERT_SORT_ASCEN:
            insertSort();
            count++;
            break;
        case DISPLAY:
            display();
            break;
        case REMOVE_FIRST:
            remove_first();
            break;
        case REMOVE_VALUE:
            remove_value();
            break;
        case COUNT:
            printf("현재 노드의 수는 %d개 입니다.\n", count);
            getchar();
            break;
        case N_VALUE:
            n_value();
            break;
        case REVERSE:
            reverse_sort();            
            break;
        case TERMINATE:
            free_node();
            printf("종료합니다.\n");
            exit(0); // 프로그램 강제 종료
        }

        printf("\n\n\t\t");
    }

    return 0;

}

//----------------------------- main함수 --------------------------------


//-----------------------여기서 부터 사용자 정의 함수----------------------

// 맨 처음 노드를 추가하는 코드
DNode* createNode() {
    DNode* newNode;
    newNode = (DNode*)malloc(sizeof(DNode));
    printf("정수 입력 : ");
    scanf("%d", &newNode->val);
    newNode->prev = NULL;
    newNode->next = NULL;
    getchar();

    return newNode;
}

void insertSort() {
    DNode* newNode = createNode();

    // 1. 연결리스트가 구성되지 않은 경우
    if (head == NULL) {
        head = newNode;
        printf("첫 노드로 추가되었습니다.\n");
        getchar();
        return;
    }

    // 2. 가장 작은 값 입력으로 첫 노드로 추가
    if (newNode->val < head->val) {
        newNode->next = head; // 새 노드와 첫 노드를 연결하기.
        head->prev = newNode; // 첫 노드의 이전 주소를 첫 노드로 설정.
        head = newNode; // 새 노드를 head로 변경하기.
        printf("가장 작은 값 입력으로 첫 노드로 추가되었습니다.\n");
        getchar();
        return;
    }

    // 3. 일반적인 경우 (마지막 노드 체크!!)
    DNode* curNode = head; // 첫 노드를 헤드로 부여.

    while (curNode->next != NULL) { // 다음 노드가 있는가?
        curNode = curNode->next;

        if (curNode->val > newNode->val) {
            newNode->next = curNode;
            newNode->prev = curNode->prev;

            newNode->prev->next = newNode;
            curNode->prev = newNode;
            printf("정렬된 순서로 노드를 삽입했습니다.\n");
            getchar();
            return;
        }
    }

    // 4. 맨 마지막으로 추가하는 경우 (curNode->next = NULL인 상태)
    newNode->prev = curNode;
    curNode->next = newNode;
    printf("마지막에 노드를 삽입했습니다.\n");
    getchar();
    return;
}

void display() { // 시간복잡도 : O(N)
    DNode* curNode; // 순회하는 목적으로 사용하는 포인터
    curNode = head; //첫 노드는 항상 head

    // 연결리스트가 구성되지 않은 경우??
    if (head == NULL || count == 0) {
        printf("연결리스트가 아직 구성되지 않았습니다.\n");
        getchar();
        return;
    }

    while(curNode->next != NULL) {
        printf("%d => ", curNode->val);
        curNode = curNode->next;
    }

    printf("%d\n", curNode->val);

    getchar();
    return;
}

void insertFront() {

    DNode* newNode = createNode();

    if (head == NULL) { // 연결리스트가 구성되지 않은 상태.
        head = newNode;
        rear = newNode;
        printf("첫 노드로 추가되었습니다.\n");
        getchar();
        return;
    }

    //1. 새로 생성된 논드와 이전 노드를 연결하는 과정
    newNode->next = head;
    head = newNode;
    head->next->prev = head;
    printf("삽입완료");
    getchar();
    return;
}


void insertRear() { // void insertRear(DNode** p) Call by Address를 실행할 시
    DNode* newNode = createNode();

    // 추가했더니 첫 노드인 경우.

    if (head == NULL || count == 0) {
        head = newNode;
        rear = newNode;
        // *p = newNode;
        printf("연결리스트가 구성되지 않아 첫 노드로 추가되었습니다.\n");
        getchar();
        return;
    }

    // 일반적인 경우

    //(*p)->next = newNode;
    rear->next = newNode;
    newNode->prev = rear;
    rear = newNode; // 새 노드를 마지막 노드로 설정
    printf("마지막 노드에 추가가 완료되었습니다.");
    getchar();
    return;
}

void remove_value() {

    int data;
    DNode* curNode;

    if (head == NULL) {
        printf("연결리스트가 구성되어있지 않습니다.\n");
        getchar();
        return;
    }

    printf("삭제할 값을 입력하세요 : ");
    scanf("%d", &data);
    getchar();

    //case 2. 삭제할 노드가 첫 노드인 경우
    if (head->val == data) {
        
        if (head->next == NULL || count == 1) { // 총 노드의 개수가 1개인 경우?? -> head밖에 없음.
            free(head); // head가 가리키는 메모리를 해제한다.
            head = NULL;
            printf("삭제할 노드가 첫 노드이므로 연결리스트는 비어있게 됩니다.\n");
            getchar();
            count--;
            return;
        }

        else { // 총 노드의 개수가 2개 이상인 경우
            head = head->next;
            free(head->prev);
            head->prev = NULL;
            printf("삭제가 완료되었습니다.\n");
            getchar();
            count--;
            return;
        }

    }

    // case 3. 일반적인 경우 (마지막 노드 체크)
    curNode = head;

    while (curNode->next != NULL) { // 다음 노드가 있을 경우

        if (curNode->val == data) {
            curNode->prev->next = curNode->next;
            curNode->next->prev = curNode->prev;
            printf("%d 값을 삭제합니다.\n", curNode->val);
            getchar();
            free(curNode);
            count--;
            return;
        }

        curNode = curNode->next; // curNode를 다음 노드로 이동
    }

    // case 4. 삭제할 노드가 마지막 노드인 경우

    if (curNode->val == data) {
        curNode->prev->next = NULL;
        rear = curNode->prev;
        printf("%d 값을 삭제합니다.\n", curNode->val);
        getchar();
        free(curNode);
        return;
    }

    printf("찾는 값이 없습니다.\n");
    getchar();
}

void free_node() {
    DNode* delNode = head;
    // 첫 노드는 항상 head
    if (head == NULL) {
        return;
    }

    // delNode에 저장한 뒤에 head 부터 순차적으로 없애는 원리
    while (delNode->next != NULL) {
        head = head->next;
        printf("동적 malloc으로 생성된 %d노드 제거\n", delNode->val);
        free(delNode);
        delNode = head;
    }

    printf("동적 malloc으로 생성된 %d노드 제거\n", delNode->val);
    free(delNode);

}

void add_Node(DNode* newNode, DNode* curNode, DNode* prevNode, int th) {
    
    // newNode의 val 값을 입력받는다.
    printf("%d번째에 새로운 값을 입력합니다. 값을 입력해주세요. : ", th);
    scanf("%d", &newNode->val);
    getchar();

    // prevNode와 curNode 사이에 newNode를 삽입하는 작업
    newNode->next = curNode;
    prevNode->next = newNode;
    newNode->prev = prevNode;
    curNode->prev = newNode;
    return;
}

void insert_kth() { // 시간복잡도 : O(N)
    DNode* newNode, *prevNode, *curNode = head;
    int th, cnt = 1;
    printf("몇 번째 노드에 값을 넣으시겠어요? : ");
    scanf("%d", &th);
    getchar();

    if (th > count + 1) {
        printf("입력하려는 위치가 노드의 길이를 초과합니다.");
        getchar();
        return;
    }

    else if (th == 1) {
        // 첫번째 노드일 경우 맨 처음에 노드를 넣는 함수로 보냄.
        printf("1번째 노드에 새로운 값을 넣습니다.");
        insertFront();
        return;
    }

    else if (th == count + 1) {
        // 맨 마지막에 추가하고 싶을 경우 맨 마지막에 노드를 넣는 함수로 보냄.
        printf("%d번째 노드에 새로운 값을 넣습니다.", count + 1);
        insertRear();
        return;
    }

    // 동적메모리 할당
    newNode = (DNode*)malloc(sizeof(DNode));
    prevNode = (DNode*)malloc(sizeof(DNode)); // 이전 노드를 저장해둘 포인터 변수

    prevNode = curNode;

    while (curNode->next != NULL) {

        if (cnt == th) {
            // 목표에 도달 했을 시 삽입작업.
            add_Node(newNode, curNode, prevNode, th);
            printf("%d번째에 새로 저장이 완료되었습니다.\n", cnt);
            getchar();
            return;
        }

        prevNode = curNode; 
        curNode = curNode->next;
        cnt++;
    }

    add_Node(newNode, curNode, prevNode, th);

    printf("%d번째에 새로 저장이 완료되었습니다.\n", cnt);
    getchar();


    return;
}

void remove_first() {

    DNode* rm_node = head;

    if (count == 0) {
        printf("삭제할 노드가 없습니다.\n");
        return;
    }
    
    else if (count == 1) {
        free(head);
        count--;
        printf("첫 노드 삭제가 완료되었습니다.\n");
        getchar();
        return;
    }

    head = rm_node->next;
    head->prev = NULL;
    free(rm_node);
    count--;
    printf("첫 노드 삭제가 완료되었습니다.\n");
    getchar();
    return;

}

void n_value() {
    int cnt , cur;
    DNode* curNode;
    // n번째 값 구하기.
    // n이 클 경우 끝에서부터, 작을 경우 앞에서부터 순회하기.
    printf("몇 번째 노드의 값을 보시겠어요? : ");
    scanf("%d", &cnt);
    getchar();

    if (cnt > count) {
        printf("노드의 길이를 초과합니다.");
        getchar();
        return;
    }

    if (cnt > count / 2) { // cnt 값이 클 경우.
        curNode = rear;
        cur = count;
        while (cur != cnt) {
            curNode = curNode->prev;
            cur--;
        }
    }
    
    else { // cnt 값이 작을 경우.
        curNode = head;
        cur = 1;
        while (cur != cnt) {
            curNode = curNode->prev;
            cur++;
        }
    }
    printf("%d번째 노드의 값은 %d 입니다.", cnt, curNode->val);
    getchar();
    return;
}

void reverse_sort() {

    if (count == 0 || count == 1) { // head == NULL || head->next == NULL
        printf("연결 리스트가 한 개 이하로 구성되어 있습니다.\n");
        getchar();
        return; // 노드 개수가 없거나 1개면 역순 정렬할 필요가 없음.
    }

    DNode *curNode, *temp;

    curNode = head;

    while (curNode != NULL) {

        temp = curNode->prev;
        curNode->prev = curNode->next;
        curNode->next = temp;

        curNode = curNode->prev; // next와 prev를 바꾸었기 때문에 다음 노드로 이동할 땐 prev로 이동한다.
        }
        
    // 마지막 노드 도달
    temp = head;
    head = rear;
    rear = temp;
    
    printf("역순 연결이 완료되었습니다.\n");
    getchar();
    return;
}
```

위와 같이 작성할 수 있다.