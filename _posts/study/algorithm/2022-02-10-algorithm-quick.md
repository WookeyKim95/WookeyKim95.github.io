---
layout: post
title:  "[알고리즘] 퀵 정렬"
subtitle:
date: 2022-02-10 09:31:22 +0900
tags: algorithm
categories: study
use_math: true
comments: true
related_posts:

---

# [알고리즘] 퀵 정렬<br/>

## 퀵 정렬이란?<br/>

리스트에서 일정한 기준 값을 정해서 기준 값보다 작은 수는 왼쪽으로 보내고, 기준 값보다 큰 수는 오른쪽으로 보내서 새로운 리스트를 형성한 다음에 재귀호출로 새로운 리스트들에 대해 위 과정을 반복하는 과정이다.<br/>

![퀵정렬과정](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/algorithm/2022-02-10_quick_1.jpg?raw=true)

이렇게 재귀호출을 반복하다가, 입력받은 리스트의 길이가 1이면 그 값을 그대로 반환하면서 길이가 1인 리스트들을 다시 합치면 정렬이 완료된다.<br/>
<br/>

## 파이썬 코드 구현<br/>

```
def quick_sort(list_input):
    if len(list_input) <= 1:
        return list_input
    
    pivot = list_input[0]
    left_list = []
    right_list = []
    
    for i in list_input[1:]:
        if i >= pivot:
            right_list.append(i)
        else:
            left_list.append(i)
            
    return quick_sort(left_list) + [pivot] + quick_sort(right_list)
```

위와 같이 구현이 가능하다.<br/>
단, 주의할 점은 return 과정에서 리스트 + 리스트 + 리스트를 연산하는 것이기 때문에 pivot이 int로 선언되었던 것을 기억하자.<br/>

`return quick_sort(left_list) + pivot + quick_sort(right_list)`<br/>

위와 같이 썼다가 오류가 일어날 수 있다.<br/>
pivot 선정기준은 그냥 편하게 리스트 맨 앞에 있는 수를 설정했지만, 어떤 수를 pivot으로 설정할지는 코드를 작성하는 사람의 마음이다.<br/>

### list Comprehension으로 코드 축약하기

```
    pivot = list_input[0]
    left_list = []
    right_list = []
    
    for i in list_input[1:]:
        if i >= pivot:
            right_list.append(i)
        else:
            left_list.append(i)
```

이 부분을

```
    pivot = list_input[0]
    left_list = [ i for i in list_input[1:] if i <= pivot ]
    right_list = [ i for i in list_input[1:] if i > pivot ]
```
아래와 같이 축약할 수 있다.<br/>
<br/>
<br/>

참고 링크 : [잔재미코딩_퀵정렬](https://fun-coding.org/Chapter15-quicksort.html)
<br/>
<br/>

## C 언어 코드 구현<br/>
<br/>

파이썬 방식과는 다르게, C에서는 재귀호출을 반복하면서 값을 바꿔주는 형식으로 정렬을 진행한다.<br/>

학원에서 배웠던 소스코드를 아래와 같이 저장한다.<br/>

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#pragma warning (disable : 4996)

#define SWAP(type, a, b) do{ type temp = a; a = b; b = temp ;}while(0)

void quick(int *arr, int left, int right);
void quick_sort(int* arr, int size);

int main() {
    int size;
    int* arr;

    printf("배열의 크기를 입력하세요 : ");
    scanf("%d", &size);

    arr = (int*)malloc(sizeof(int) * size);

    srand(time(NULL));

    for (int i = 0; i < size; i++) {
        arr[i] = rand() % 200 - 100; // -100 ~ 100 까지의 난수 생성
        printf("%d ", arr[i]);
    }
    puts("");

    for (int i = 0; i < size; i++) {
        if (arr[i] < 0) {
            arr[i] *= -1; // -1을 곱하면 그것이 곧 절댓값이다.
        }
    }

    quick_sort(arr, size);

    for (int i = 0; i < size; i++) {
        printf("%d ", arr[i]);
    }
    puts("");
    
    free(arr);
}

void quick(int *arr, int left, int right) {
    int p1 = left;
    int p2 = right;

    int pivot = arr[(left + right) / 2];

    while (p1 <= p2) {
        while (arr[p1] < pivot) {
            p1++; // 좌측의 포인터가 피벗쪽으로 이동하도록 유도
            // (이때, 좌측 포인터가 가리키는 값이 피벗값보다 크면 중지)
        }

        while (arr[p2] > pivot) {
            p2--; // 우측의 포인터가 피벗쪽으로 이동하도록 유도
            // (이때, 우측 포인터가 가리키는 값이 피벗값보다 작으면 중지)
        }

        if (p1 <= p2) {
            // 좌측의 포인터와 우측의 포인터가 서로 반대쪽으로 가지 않았는지 체크
            // 반대쪽으로 갔으면 정렬이 완료된 것.
            // 정상적 상태라면 두 포인터가 가리키는 값을 서로 바꿔주기.

            SWAP(int, arr[p1], arr[p2]);
            p1++; // 바꾸고 다시 제 갈길 가기.
            p2--; // 바꾸고 다시 제 갈길 가기.
        }
    }

    if (p2 > left) { // 왼쪽 배열들 부터 재귀 반복
        quick(arr, left, p2);
    }

    if (p1 < right) { // 왼쪽이 완료된 이후에 오른쪽 배열들을 재귀 반복함.
        quick(arr, p1, right);
    }

}

void quick_sort(int* arr, int size) {
    quick(arr, 0, size - 1);
}
```