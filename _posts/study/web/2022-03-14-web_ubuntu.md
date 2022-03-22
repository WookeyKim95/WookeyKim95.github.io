---
layout: post
title:  "[Linux] 간단한 명령어 정리"
subtitle:  
date: 2022-03-14 09:04:42 +0900
categories: study
tags: web_&_frontend
comments: true
related_posts:

---

# [Linux] 간단한 명령어 정리<br/>

## 개발에서 리눅스는.. <br/>
<br/>

코드 리뷰를 받으면서 리눅스에 관한 이야기도 나누어보았고, 리눅스, 우분투를 구동할 줄 아는 것이<br/>

웹 개발자로서의 필수 역량이라는 것을 깨달았다.<br/>

이유를 생각해보니 사실 웹 개발도 물론이고 개발영역 전체에 있어서 리눅스 OS에서는 윈도우 보다<br/>

메모리 사용이 효율적이고 실행속도가 빠르기 때문에<br/>

거의 필수로 자리잡고 있었다.<br/>

윈도우에서는 WSL2를 설치하고 우분투를 이용하여 가상의 리눅스 환경을 구축할 수 있다.<br/>
<br/>

## 명령어 정리<br/>
<br/>

그러면 간단하게 기초 명령어 몇 가지를 기록해두어야겠다.<br/>
<br/>

참고 : [유튜브_얄팍한 코딩사전](https://www.youtube.com/watch?v=tPWBF13JIVk)

### pwd<br/>

현재 위치를 출력한다.<br/>
<br/>

### ls<br/>

현재 위치에서의 폴더, 파일목록을 출력한다.<br/>
list의 약어라고 생각하면 편할듯.<br/>
<br/>

### cd XXX<br/>

XXX 디렉토리로 이동한다.<br/>

```
# 예시
cd linux_hello

# linux_hello 폴더로 이동한다.

cd ../

# 현재 위치의 상위 폴더로 이동한다.
```

<br/>

### mkdir XXX<br/>

XXX 디렉토리를 현재 경로에서 생성한다.<br/>

```
# 예시
mkdir linux_hello
```

<br/>

### vi<br/>

텍스트 파일을 만든다.<br/>

```
# 예시
vi hello.txt

# 이후 ls를 입력하면
hello.txt
```

### cp<br/>

파일을 복사한다.<br/>

```
# 예시
# 현재 상태
ls
hello.txt

# 복사 실행
cp hello.txt hello2.txt
ls

# 결과
hello.txt hello2.txt
```

### mv<br/>

```
# 예시

mv hello.txt ../
```

이렇게 하면 상위폴더로 hello.txt 파일이 이동한다.<br/>

앞에는 대상 파일 이름, 뒤에는 경로를 적어주면 된다.<br/>

### rm<br/>

```
# 예시

rm hello2.txt
```

대상 파일의 이름을 적어주면 해당 파일이 삭제된다.<br/>
