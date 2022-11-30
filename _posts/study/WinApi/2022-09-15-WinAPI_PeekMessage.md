---
layout: post
title:  "[WinAPI] 윈도우의 동작, 그리고 PeekMessage"
subtitle:  
date: 2022-09-15 09:11:19 +0900
categories: study
tags: WinAPI&DirectX
comments: true
related_posts:

---

# [WinAPI] 윈도우의 동작, 그리고 PeekMessage<br/>
<br/>

본격적으로 윈도우 프로그램이 동작하는 원리를 알아보자.<br/>

## 윈도우 프로그램이 동작하는 원리는?<br/>
<br/>

![대략 구동](https://github.com/WookeyKim95/WookeyKim95.github.io/blob/main/assets/img/study/WinAPI/2022-09-15_1.jpg?raw=true)

<br/>
대략 위와 같이 크게 3단계 작업으로 생각할 수 있다.<br/>

<br/>
동적 할당 등 윈도우 실행을 위한 함수가 실행되고 난 뒤에,<br/>

무한루프 문을 돌면서 화면을 갱신하는 렌더링 작업을 계속한다.<br/>

그리고 종료 조건을 달성하면 break문을 통해서 무한루프문을 빠져나오고,<br/>

동적할당 된 메모리를 해제 하는 등 종료작업을 거쳐서 프로그램이 종료되는 원리.<br/>

<Br/>
여기서 우리가 주목하면 좋은 곳은 렌더링을 진행하는 무한루프 문으로, <br/>

해당 부분을 아래와 같이 예시를 작정할 수 있다.<br/>

```
while (true)
    {
        if (PeekMessage(&msg, nullptr, 0, 0, PM_REMOVE))
        {
            if (WM_QUIT == msg.message)
                break;

            // 단축키 조합 확인
            if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg))
            {
                TranslateMessage(&msg); // 분석
                DispatchMessage(&msg);  // 처리
            }
        }

        else
        {
            // Game Run
            CEngine::GetInst()->progress();
        }      
    }
```

어라? Visual Studio에서 새로운 프로젝트를 만들어서 실행시키면 나오는 무한루프문과는 다르게 생겼다.<br/>

여기서 PeekMessage라는 부분이 보인다. 이건 무엇일까?<br/>
<br/>

## PeekMessage<br/>
<Br/>

살펴보기전에, 새로운 프로젝트를 만들면 나타나는, 기존의 메세지 기반 코드의 무한루프 문을 살펴보자.<br/>

```
while (GetMessage(&msg, nullptr, 0, 0))
{
	// 단축키 조합 확인
	if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg))
	{
		TranslateMessage(&msg); // 분석
        DispatchMessage(&msg);  // 처리
	}
}
```

두 무한 루프문의 차이점으로 기존의 코드는 GetMessage함수, 변경된 코드는 PeekMessage함수를 사용한다는 점이다.<br/>
<Br/>

컴퓨터는 보통 상태에서 1초당 렌더링을 3만번 정도, 성능이 좋은 컴퓨터는 17만번 까지 처리를 하기도 한다.<br/>

1초에 수만번을 처리하는 동안 마우스를 클릭하거나 키보드 키를 누르는 순간은 몇 만분의 1초에 불과한 것이다.<br/>

여기서 GetMessage와 PeekMessage의 차이점을 살펴보자.<br/>
<br/>

먼저, **GetMessage 함수는 메세지가 무엇이 들어왔는지를 판단한다.<br/>**
<br/>
여기서 말하는 메세지는 쉽게 생각하면 아래와 같이 분류가 가능하다.

- 마우스 버튼을 눌렀을 때 해당 키
- 마우스를 드래그했는가
- 키보드를 눌렀을 때 해당 키
- 아무런 행동도 취하지 않음.

즉, 아무런 행동도 취하지 않았을 때도 메세지가 들어온 것으로 간주하여 렌더링을 하면서 동시에 메세지와 그를 따라가는 코드들을 계속해서 처리한다.<br/>
<br/>

반면, **PeekMessage 함수는 메세지가 들어왔는가 여부, 즉 아무 키를 눌렀는지 판단한다.<br/>**
<br/>

```
        if (PeekMessage(&msg, nullptr, 0, 0, PM_REMOVE))
        {
            if (WM_QUIT == msg.message)
                break;

            // 단축키 조합 확인
            if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg))
            {
                TranslateMessage(&msg); // 분석
                DispatchMessage(&msg);  // 처리
            }
        }

        else
        {
            // Game Run
            CEngine::GetInst()->progress();
        }
```

그래서 무한루프 내의 함수가 위와 같이 생긴 것이다.<br/>

메세지가 들어왔다면 if 문 안에 들어가서 해당 메세지에 맞는 코드를 처리하고, 그렇지 않으면 렌더링만을 계속한다.<br/>
<br/>

## 어느 것이 더 효율적일까??<br/>
<br>

PeekMessage 기반 처리방법이 더 효율적이다.<br/>

위에 언급했듯이 1초당 수만번의 작업 도중에 렌더링과 어떤 메세지인지 확인하고, 메세지를 처리하는 작업을 동시에 계속하는 방법과, <br/>
메세지 처리 작업과 렌더링 작업을 분리하는 것 중에서는 후자가 더 효율적이기 때문이다.<br/>