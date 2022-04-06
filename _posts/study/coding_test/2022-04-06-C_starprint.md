---
layout: post
title:  "조건만큼 별 출력하기 C"
subtitle:   
date: 2022-04-06 09:05:07 +0900
tags: coding_test
categories: study
use_math: true
comments: true
related_posts:

---

# 조건만큼 별 출력하기 C<br/>

## C에서 같은 문자 반복출력하기<br/>
<br/>

문자열을 입력받아 대문자, 소문자, 숫자문자, 기타문자의 개수만큼 별표를 찍는 프로그램을 작성하자.<br/>

```


#include <stdio.h>

#pragma warning (disable :4996)

void output(int i) {
    for (int j = 0; j <i; j++) {
        printf("*");
    }
    printf("\n");
}

int main() {
    char c[255];
    int r[4] = {0, 0, 0, 0};

    printf("문자열 입력: ");
    fgets(c, 254, stdin);

    for (int i = 0; c[i] != '\0'; i++) {
        if ('A' <= c[i] && c[i] <= 'Z') {
            // 대문자인 경우
            ++r[0];
        }
        else if ('a' <= c[i] && c[i] <= 'z') {
            // 소문자인 경우
            ++r[1];
        }
        else if ('0' <= c[i] && c[i] <= '9') {
            // 숫자인 경우
            ++r[2];
        }
        else {
            // 기타인 경우
            ++r[3];
        }
    }

    printf("대 문 자 %d개 : ", r[0]);
    output(r[0]);
    printf("소 문 자 %d개 : ", r[1]);
    output(r[1]);
    printf("숫자문자 %d개 : ", r[2]);
    output(r[2]);
    printf("기타문자 %d개 : ", r[3]);
    output(r[3]);
}


```

## 주목할 부분<br/>
<br/>

```
for (int i = 0; c[i] != '\0'; i++)
```
이 부분을 통해서 null문자를 감지해서 원하는 갯수만큼만 출력할 수 있다.<br/>