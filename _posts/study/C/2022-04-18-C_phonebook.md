---
layout: post
title:  "[C] 기초실습_전화번호부 관리 코드"
subtitle:
date: 2022-04-18 09:36:24 +0900
tags: C
categories: study
comments: true
related_posts:

---

# [C] 기초실습_전화번호부 관리 코드<Br/>

강의에서 배운 C언어 기초내용을 이용해서 전화번호부 관리 프로그램을 만들어보기.<br/>

코드 보관 및 공유를 위해서 작성한 포스트.<br/>
<br/>

## 메인 코드<br/>
<br/>

```
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include "myheader.h" //clear_buffer, remove_enter 함수 import

#define NAME_LEN 20 // 이름의 최대 길이
#define NUM_LEN 20 // 전화번호 최대 길이
#define TEL_SIZE 100 // 전화번호는 최대 100개까지 저장하기로함.

enum Tel_menu{ // switch, case문에서 사용하기 위한 열거형
            // 보통 관련있는 매크로 상수가 여러개 필요한 경우 사용
    // 0부터 차례대로 상수값이 할당된다. (ADD : 0, DISPLAY : 1, ...., TERMINATE : 5)
    // 하지만 맨 처음을 1로 할당되면 (DISPLAY : 2, ... , TERMINATE : 6) 과 같이 할당된다.
    // 즉, 할당된 상수값부터 1씩 증가한다.
    ADD = 1, DISPLAY, MODIFY, REMOVE, SEARCH, REMOVE_ALL, /*FILE_SAVE, FILE_OPEN,*/ TERMINATE = 0
    // 아래에 있는 코드 처럼 할당하고 싶으면 위와 같이 하거나 TERMINATE를 맨 앞으로 보내면 된다.
};

typedef struct Telephone {
    char name[NAME_LEN];
    char phone[NUM_LEN];
}Tel;

void _sort(Tel* tel, int index);
void print_title();
void addition(Tel* tel, int* index);
void display(Tel* tel, int IDX);
void file_save(Tel* tel, int index);
void file_open(Tel* tel, int* IDX);
void _sort(Tel* tel, int index);
void _search(Tel* tel, int IDX);
void _modify(Tel* tel, int IDX);
void _remove(Tel* tel, int* IDX);

int main() {
    Tel tel[TEL_SIZE]; // 전화번호 변수 선언 및 전화번호부의 크기 지정
    int index = 0; // 배열에 저장하려는 위치
    int choice;

    file_open(tel, &index);

    while (TRUE) {
        printf("\n#### 전화번호부 관리 프로그램 ####\n\n");
        printf("1. 전화번호 등록\n");
        printf("2. 전화번호 리스트 출력\n");
        printf("3. 전화번호 수정\n");
        printf("4. 전화번호 삭제\n");
        printf("5. 전화번호 검색\n");
        printf("6. 전화번호 전체 삭제\n");
        /*
        printf("7. 전화번호 파일 저장\n");
        printf("8. 전화번호 파일 불러오기\n");
        */
        printf("0. 프로그램 종료\n");
        
        printf("\n메뉴선택 : [ ]\b\b");
        scanf("%d", &choice);
        clear_buffer();

        switch (choice)
        {
        case ADD: // 전화번호 추가
            addition(tel, &index); // call by address
            break;
        case DISPLAY: // 전화번호 출력
            display(tel, index); // call by value
            break;
        case MODIFY: // 전화번호 수정
            _modify(tel, index);
            break;
        case REMOVE: // 전화번호 삭제
            _remove(tel, &index);
            break;
        case SEARCH: // 전화번호 검색
            _search(tel, index);
            break;
        case REMOVE_ALL: // 전화번호 전체삭제
            index = 0;
            printf("데이터가 모두 삭제되었습니다.");
            break;
        /*
        case FILE_SAVE: // 전화번호 파일 저장, 프로그램 종료시마다 저장하도록 주석처리
            file_save(tel, index);
            break;
        case FILE_OPEN: // 전화번호 파일 열기, 프로그램 작동시마다 불러오도록 주석처리
            break;
        */
        case TERMINATE:
            file_save(tel, index);
            exit(0); // 프로그램 종료
            break;
        default:
            break;
        } // switch end
        printf("\n\n\t\t");
        system("pause"); // 일시정지
        system("clear"); // 화면지우기
    } // while end
    return 0;
}

void print_title() {
    // 제목 출력
    printf("#### 전화번호 리스트 출력 ####");
    puts("");
    printf("%4s. %-20s %-20s\n", "번지", "이름", "전화번호");
}

void _sort(Tel* tel, int index) {
    int i, j;
    Tel temp;
    for (i = 0; i < index - 1; i++) { // 정렬 기준
        for (j = i + 1; j < index; j++) { // 비교 데이터
            if (strcmp(tel[i].name, tel[j].name) > 0) { // strcmp함수 : 앞 코드값이 크면 양수, 뒤 코드 값이 크면 음수, 같으면 0 리턴
                temp = tel[i];
                tel[i] = tel[j];
                tel[j] = temp;
            }
        }
    }
}

void addition(Tel* tel, int* IDX) { // 전화번호 저장 함수
    system("clear");

    if (*IDX >= TEL_SIZE) { // 전화번호부가 꽉 찼을경우
        printf("더 이상 저장할 수 없습니다. 데이터 삭제 후 진행하세요.\n");
        printf("진행하려면 아무키나 누르세요.\n");
        getchar();

        // 호출한 곳으로 돌아가기
        return;
    }

    printf("#### 전화번호 등록 ####\n");
    printf("이름을 입력하세요.\n");
    fgets(tel[*IDX].name, NAME_LEN, stdin);
    remove_enter(tel[*IDX].name);

    puts("");

    printf("전화번호를 입력하세요.\n");
    fgets(tel[*IDX].phone, NUM_LEN, stdin);
    remove_enter(tel[*IDX].phone);

    (*IDX)++; // 다음 지점을 가리키도록 1씩 증가시켜줌.
    printf("%d 번지 등록이 완료되었습니다.\n", *IDX);
    printf("남은 저장 공간 : %d개", TEL_SIZE - *IDX);

    _sort(tel, *IDX); // 저장 후 정렬을 위한 코드.

    getchar(); // 대기를 위한 코드
}

void display(Tel* tel, int IDX) { // 전화번호 출력 함수
    system("clear");
    
    if (IDX == 0) { // index가 0이면 전체삭제가 된다.
        printf("저장된 데이터가 없습니다.\n");
        printf("진행하려면 아무키나 누르세요.\n");
        clear_buffer();
        return;
    }

    //제목 출력
    print_title();

    // 이름, 전화번호 출력
    for (int i = 0; i < IDX; i++) {
        printf(" %03d. %-20s %-20s\n", i + 1, tel[i].name, tel[i].phone);
    }

    getchar(); // 대기를 위한 코드
}

void file_save(Tel* tel, int IDX) {
    FILE* fp;
    fp = fopen("telephone.txt", "w");

    if (fp == NULL) {
        printf("파일오픈 실패");
        return; // 메뉴로 돌아가기
    }

    for (int i = 0; i < IDX; i++) {
        fprintf(fp, "%s %s\n", tel[i].name, tel[i].phone);
    }

    printf("telephone.txt에 저장이 완료되었습니다.\n");
    clear_buffer(); // 대기 목적

    fclose(fp);
}

void file_open(Tel* tel, int* IDX) {
    FILE* fp;
    fp = fopen("telephone.txt", "r");

    if (fp == NULL) {
        printf("파일오픈 실패");
        getchar(); // 대기 목적
        return; // 메뉴로 돌아가기
    }

    *IDX = 0; // 데이터를 불러오는 과정에서 인덱스가 증가해야함.
    // 얼만큼? 파일에 저장되어 있는 만큼
    // 그러면서 프로그램 상에서 저장하기 위한 장치.

    while (fscanf(fp, "%s %s", tel[*IDX].name, tel[*IDX].phone) != EOF) {
        ++*IDX; // 혹시 *IDX++로 했는데 세그먼트 오류 발생할경우.
    }

    printf("telephone.txt 불러오기가 완료되었습니다.\n");
    clear_buffer(); // 대기 목적
    fclose(fp);
}

void _search(Tel* tel, int IDX) {
    int i;
    char search_name[NAME_LEN];
    int flag = FALSE;

    system("clear");
    printf("찾을 이름을 입력하세요.\n");
    fgets(search_name, NAME_LEN, stdin);
    remove_enter(search_name);

    for (i = 0; i < IDX; i++) {
        if (strcmp(tel[i].name, search_name) == 0) { // 문자열을 비교하는 함수!! 같으면 0을 반환함!
            print_title();
            printf(" %03d. %-20s %-20s\n", i + 1, tel[i].name, tel[i].phone);
            flag = TRUE;
            break;
        }
    }

    if (flag == FALSE) {
        printf("해당 이름이 없습니다.\n");
    }

    printf("진행하려면 아무키나 누르세요.\n");
    clear_buffer();

    return;
}

void _modify(Tel* tel, int IDX) {
    int i;
    char choose;
    char search_name[NAME_LEN];
    char temp[NAME_LEN]; // 수정시 본래 데이터를 보호하기 위해 임시변수 지정
    int flag = FALSE;

    system("clear");
    printf("수정할 이름을 입력하세요.\n");
    fgets(search_name, NAME_LEN, stdin);
    remove_enter(search_name);

    for (i = 0; i < IDX; i++) {

        if (strcmp(tel[i].name, search_name) == 0) { // 문자열을 비교하는 함수!! 같으면 0을 반환함!

            print_title();
            printf(" %03d. %-20s %-20s\n", i + 1, tel[i].name, tel[i].phone);
            flag = TRUE;

            printf("이름 수정 : 0 / 전화번호 수정 : 1\n"); // 원하는 것을 선택해서 변경하도록 하는 장치
            choose = fgetc(stdin); // 문자로 받음.
            clear_buffer(); // 버퍼에 남아있는 엔터를 삭제해서 오류 방지
            
            if (choose == '0') {
                printf("새로 입력할 이름을 입력하세요. (취소시 #)\n");
                fgets(temp, NAME_LEN, stdin);
                remove_enter(temp);
                if (temp[0] == '#' && temp[1] == '\0'){
                    // ### 과 같은 예외 방지 오직 #을 입력했을 시에만 취소처리.
                    printf("취소합니다.\n");
                    break;
                }
                else {
                    strcpy(tel[i].name, temp); // 배열을 복사하는 함수
                }
                
            }  

            else if (choose == '1') {
                printf("새로 입력할 전화번호를 입력하세요. (취소시 #)\n");
                fgets(temp, NAME_LEN, stdin);
                remove_enter(temp);

                if (temp[0] == '#' && temp[1] == '\0'){
                    // ### 과 같은 예외 방지 오직 #을 입력했을 시에만 취소처리.
                    printf("취소합니다.\n");
                    break;
                }
                else {
                    strcpy(tel[i].phone, temp); // 배열을 복사하는 함수

                }
            }

            printf("수정이 완료되었습니다.\n");

            break; // 변경 후 반복문 탈출
        }
    }

    if (flag == FALSE) {
        printf("해당 이름이 없습니다.\n");
    }

    printf("진행하려면 아무키나 누르세요.\n");
    clear_buffer();

    return;
}

void _remove(Tel* tel, int* IDX) {
    int i, j;
    char search_name[NAME_LEN];
    int flag = FALSE;

    system("clear");
    printf("삭제할 이름을 입력하세요.\n");
    fgets(search_name, NAME_LEN, stdin);
    remove_enter(search_name);

    for (i = 0; i < *IDX; i++) {
        if (strcmp(search_name, tel[i].name) == 0) {
            for (j = i; j < *IDX - 1; j++) {
                tel[j] = tel[j+1];
            }
            
            *IDX--;
            flag = TRUE;
            printf("삭제가 완료되었습니다.\n");
            break;
        }
    }

    if (flag == FALSE) {
        printf("해당 이름이 없습니다.\n");
    }

    printf("진행하려면 아무키나 누르세요.\n");
    clear_buffer();

    return;
}


```

<br/>

## 헤더파일 코드<br/>
<br/>

```
#include <stdio.h>
#include <string.h>

#pragma once // include가 중복되지 않도록 막는 역할 (include를 여러번 해도 한번만 include 되도록 하는 역할.)
#pragma warning (disable : 4996)

#define TRUE 1
#define FALSE 0

void clear_buffer();
void remove_enter(char* in);

void clear_buffer() {
    while (getchar() != '\n');
}

void remove_enter(char* in) {
    // fgets 사용으로인해 입력된 \n을 \0으로 변환하기 위한 코드
    int len = strlen(in);
    in[len - 1] = '\0';
}
```