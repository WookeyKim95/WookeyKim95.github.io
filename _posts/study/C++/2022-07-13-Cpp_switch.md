---
layout: post
title:  "[C++] switch문과 enum의 사용"
subtitle:  
date: 2022-07-13 14:45:11 +0900
categories: study
tags: C++
comments: true
related_posts:

---

# [C++] switch문과 enum의 사용<br/>
<br/>

switch 문은 if문과 더불어 분기 실행 역할을 하는 구문이다.<br/>

차이점이라면, if문은 분기에 논리연산을 사용하지만 swtich문은 변수와 상수를 주로 사용한다는 점이다.


## switch문의 형태

```
switch (변수)
{
	case (상수1):
		실행코드
		break;
	case (상수2):
		실행코드
		break;
	case (상수3):
		실행코드
		break;
	default:
		실행코드
		break;
}
```

여기서 주의할점은 실행코드 밑에 break문을 붙이지 않으면 그 다음 case에 있는 코드까지 실행된다는 점이다.<br/>

```
switch (변수)
{
	case (1):
		std::cout << 1 << std::endl;
	case (2):
		std::cout << 2 << std::endl;
		break;
	case (3):
		실행코드
		break;
	default:
		실행코드
		break;
}
```

변수 = 1일때 실행결과 <br/>

```
1
2
```

하지만 오히려 이를 역이용해서 같은 케이스에 대해서 처리를 해주는 경우도 있다.<br/>

아래와 같이 예시코드를 작성할 수 있다.<br/>

```
switch (변수)
{
	case (1):
		std::cout << 1 << std::endl;
		break;
	case (2):
		std::cout << 2 << std::endl;
		break;
	case (3):
	case (4):
		std::cout << "3과 4는 같습니다." << std::endl;
		break;
	default:
		실행코드
		break;
}
```

이렇게하면 변수가 3과 4일경우 서로 같은 결과가 출력된다. 게임에서도 이런 경우를 쓸 때가 있겠지.

## enum문 (열거체)<br/>
<br/>

switch문과 연계를 해서 쓸 수 있는 것이 있는데, 바로 enum문 (열거체)이다.<br/>

```
enum = {A, B, C}

switch (val)
{
	case (A):
		std::cout << "A" << std::endl;
		break;
	case (B):
		std::cout << "B" << std::endl;
		break;
	case (C):
		std::cout << "C" << std::endl;
		break;
	default:
		실행코드
		break;
}
```

맨 처음 자리는 지정을 하지 않을 경우 0으로 취급한다.<br/>
그리고 순서대로 1씩 증가하여 swtich문에서 val의 값에 따라서 서로 다른 결과를 출력 시킬 수 있다.<br/>

```
출력 결과

(val = 0)
A

(val = 1)
B

(val = 2)
C
```

<br/>

그리고 아래와 같이 열거체에다가 상수를 부여할 수 있다.<br/>

```
	enum Test
	{
		Test1 (이때 Test1은 0),
		Test2 = 30,
		Test3 (이때 Test3은 31),
		Test4 = 90,
		Test5 (이때 Test5는 91)
	};
```
<br/>

## 열거체의 게임에서의 사용 예시<br/>
<br/>

```
enum JOB { KNIGHT = 1, MAGICIAN, ARCHER};

	int job;

	cout << "직업을 선택하세요" << endl;
	cout << "기사 : 1, 마법사 : 2, 궁수 : 3" << endl;
	cin >> job;

	switch (job)
	{
	case KNIGHT:
		cout << "기사입니다." << endl;
		break;
	case MAGICIAN:
		cout << "마법사입니다." << endl;
		break;
	case ARCHER:
		cout << "궁수입니다." << endl;
		break;
	default:
		cout << "다시 선택해주세요." << endl;
	}
```

1번을 입력하면 기사, 2번을 입력하면 마법사, 3번을 입력하면 궁수가 되는 형태로 코드를 짤 수 있다.<br/>
<br/>

그리고 아래와 같이 2진법을 이용해서 버프의 상태를 체크하는 곳에서도 사용이 가능해진다.<br/>

```
	enum BUF
	{
		BUF_ATTACK = 0x1,
		BUF_ARMOR = 0x2,
		BUF_HP = 0x4,
		BUF_MP = 0x8,
		BUF_CRITICAL = 0x10,
	};
```
<br/>

## enum class<br/>
<br/>

새로나온 열거체 문법으로, enum을 객체로 만들어내는 방식이다.<br/>
보통 enum은 정수형을 이용하기 때문에 자료형의 크기가 항상 4이지만, 아래와 같이 코드를 짜면 자료형의 형태를 조절하여 크기를 조절할 수 있다.<br/>

```
	enum class Test_type : unsigned char
	{
		Test1,
		Test2,
		Test3,
	};
```

enum 과 enum class를 이용하면 case에 이름을 넣어줄 수 있어서 가독성이 좋고 나중에 수정을 하기가 용이해진다.<br/>
<Br/>

## enum class 활용 : 가위바위보<br/>
<br/>

난수와 enum class, switch문을 이용해서 가위바위보 코드를 짜보았다.<br/>

```
# include <iostream>
# include <ctime>

enum class rsp
{	Scissor = 1, // 가위
	Rock, // 바위
	Paper, // 보
	End // 종료
};

int main()
{
	int mine;
	int AI_ans;

	srand((unsigned int)time(0));

	for (;;)
	{	
		std::cout << "1. 가위" << std::endl;
		std::cout << "2. 바위" << std::endl;
		std::cout << "3. 보" << std::endl;
		std::cout << "4. 종료" << std::endl;
		std::cin >> mine;

		AI_ans = (rand() % 3) + 1;

		switch (mine)
		{
		case ((int)rsp::Scissor):

			if (mine == AI_ans)
				std::cout << "나는 가위! 컴퓨터는 가위로 무승부!" << std::endl;
			else if (AI_ans == (int)rsp::Rock)
				std::cout << "나는 가위! 컴퓨터는 바위로 패배!" << std::endl;
			else if (AI_ans == (int)rsp::Paper)
				std::cout << "나는 가위! 컴퓨터는 보로 승리!" << std::endl;
			break;

		case ((int)rsp::Rock):

			if (mine == AI_ans)
				std::cout << "나는 바위! 컴퓨터는 바위로 무승부!" << std::endl;
			else if (AI_ans == (int)rsp::Scissor)
				std::cout << "나는 바위! 컴퓨터는 가위로 승리!" << std::endl;
			else if (AI_ans == (int)rsp::Paper)
				std::cout << "나는 바위! 컴퓨터는 보로 패배!" << std::endl;
			break;

		case ((int)rsp::Paper):
			if (mine == AI_ans)
				std::cout << "나는 보! 컴퓨터는 보로 무승부!" << std::endl;
			else if (AI_ans == (int)rsp::Rock)
				std::cout << "나는 보! 컴퓨터는 바위로 승리!" << std::endl;
			else if (AI_ans == (int)rsp::Scissor)
				std::cout << "나는 보! 컴퓨터는 가위로 패배!" << std::endl;
			break;

		case ((int)rsp::End):
			std::cout << "프로그램을 종료합니다." << std::endl;
			return 0; // 프로그램을 곧바로 종료하기 위한 코드
			break;

		default:
			std::cout << "유효한 수를 입력해주세요." << std::endl; // continue 역할.
			break;
		}
	}
	return 0;
}
```
<br/>

실행결과<br/>

```
1. 가위
2. 바위
3. 보
4. 종료
1
나는 가위! 컴퓨터는 보로 승리!
1. 가위
2. 바위
3. 보
4. 종료
1
나는 가위! 컴퓨터는 가위로 무승부!
1. 가위
2. 바위
3. 보
4. 종료
2
나는 바위! 컴퓨터는 가위로 승리!
1. 가위
2. 바위
3. 보
4. 종료
3
나는 보! 컴퓨터는 가위로 패배!
1. 가위
2. 바위
3. 보
4. 종료
2
나는 바위! 컴퓨터는 보로 패배!
1. 가위
2. 바위
3. 보
4. 종료
3
나는 보! 컴퓨터는 보로 무승부!
1. 가위
2. 바위
3. 보
4. 종료
4
프로그램을 종료합니다.
```