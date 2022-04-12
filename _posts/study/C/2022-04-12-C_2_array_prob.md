---
layout: post
title:  "[C] 2차원 배열 예제풀기"
subtitle:
date: 2022-04-12 09:38:14 +0900
tags: C
categories: study
comments: true
related_posts:

---

# [C] 2차원 배열 예제풀기<Br/>

## 예제를 몇개 풀어보자.<br/>
<br/>

### 구구단 출력하기<br/>
<Br/>

2차원 배열에 대한 기초코딩테스트의 단골문제이다.<br/>
2단부터 9단까지의 구구단을 출력하시오.<br/>

C언어에서는 아래와 같이 코드를 짤 수 있다.

```
#include <stdio.h>

#pragma warning (disable : 4996)

#define row 8
#define col 9

int main() {
    int i, j, num = 1;
    int a[row][col];

    // 저장
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++){
            a[i][j] = (i + 2) * (j + 1);
        }
    }

    // 출력
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++){
            printf("%5d", a[i][j]);
        }
        puts("");
    }
    return 0;
}
```

그리고 결과는 아래와 같다.<br/>

```
    2    4    6    8   10   12   14   16   18
    3    6    9   12   15   18   21   24   27
    4    8   12   16   20   24   28   32   36
    5   10   15   20   25   30   35   40   45
    6   12   18   24   30   36   42   48   54
    7   14   21   28   35   42   49   56   63
    8   16   24   32   40   48   56   64   72
    9   18   27   36   45   54   63   72   81
```
<br/>

### 난수입력 후 마지막 열에 합 출력하기<br/>
<br/>

```
    3    6    7    5    3   24
    5    6    2    9    1   23
    2    7    0    9    3   21
    6    0    6    2    6   20
    1    8    7    9    2   27
    0    2    3    7    5   17
```
위와 같이 1열부터 5열까지는 0부터 9까지의 정수를,
6열에 5개 숫자의 합을 저장하고 출력하는 코드를 짜보자.<br/>

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#pragma warning (disable : 4996)

#define row 6
#define col 6

int main() {
    int i, j, num = 1;
    int a[row][col];

    for (i = 0; i < row; i++) {
        int sum = 0;
        for (j = 0; j < col - 1; j++) {
            a[i][j] = rand() % 10; // 0 ~ 9
            sum += a[i][j];
        }
        a[i][j] = sum;
    }
    // 출력
    for (i = 0; i < row; i++) {
        for (j = 0; j < col; j++){
            printf("%5d", a[i][j]);
        }
        puts("");
    }
    return 0;
}
```

### 점수 막대 그래프 출력하기<br/>
<br/>

한 배열에는 정답이,<br/>
다른 2차원 배열에는 학생들의 답안이 입력되어있다.<br/>

채점후에 막대그래프로 출력하여라.<br/>

```
#include<stdio.h>
#pragma warning (disable : 4996)

#define std_num 5
#define prob_num 10


int main()
{
    int i, j;
	int ans[prob_num] = { 3, 2, 1, 3, 4, 1, 2, 3, 4, 1 }; //정답

	int ans_std[std_num][prob_num] = { //5명 학생의 답안
		{3, 2, 1, 3, 4, 1, 2, 3, 4, 1}, //1번 답안
		{1, 2, 1, 3, 4, 2, 2, 3, 4, 1}, //2번 답안
		{2, 2, 1, 2, 4, 1, 2, 1, 4, 1}, //3번 답안
		{3, 1, 1, 3, 3, 1, 2, 3, 1, 4}, //4번 답안
		{4, 2, 1, 3, 4, 1, 2, 3, 4, 1} //5번 답안
	};
	int score[std_num] = { 0 }; //5명 학생의 점수

    // 저장

    for (i = 0; i < std_num; i++) {
        int std_score = 0;
        for (j = 0; j < prob_num; j++) {
            if (ans_std[i][j] == ans[j]) {
                score[i] += 1;
            }
        }
    }

    // 출력

        printf(" 점 수 출 력 \n\n");
    for (i = 0; i < std_num; i++) {
        printf("%d번 학생 점수 : %d\n", i+1 , score[i] * 10);
    }
    puts("");

    
    printf(" 출 력 결 과 \n\n");
    for (j = 0; j < prob_num; j++){

        for (i = 0; i < std_num + 1; i++) {
            if (i == 0){
                printf("%3d", 100 - (j * 10));
                continue;
            }
            if (score[i - 1] * 10 >= (100 - j * 10)) {
                printf("%3c", '*');
            }
            else {
                printf("   ");
            }
            
        }
        puts("");

    }
    printf("======================\n");
    printf("번호  ");
    for (i = 0 ; i < std_num; i++) {
        printf("%d  ", i+1);
    }
    puts("");
}

```

그리고 출력결과는 아래와 같다.<br/>

```
1번 학생 점수 : 100
2번 학생 점수 : 80
3번 학생 점수 : 70
4번 학생 점수 : 60
5번 학생 점수 : 90

 출 력 결 과 

100  *            
 90  *           *
 80  *  *        *
 70  *  *  *     *
 60  *  *  *  *  *
 50  *  *  *  *  *
 40  *  *  *  *  *
 30  *  *  *  *  *
 20  *  *  *  *  *
 10  *  *  *  *  *
======================
번호  1  2  3  4  5  
```