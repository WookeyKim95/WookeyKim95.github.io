---
layout: post
title:  "[클론프로젝트] 스타벅스코리아 2일차"
subtitle:
date: 2022-02-25 20:01:38 +0900
categories: project
tags:
comments: true
related_posts:

---

## 스타벅스 코리아 홈페이지 만들기 2일차

[내 프로젝트 깃허브 저장소 링크](https://github.com/WookeyKim95/clone_StarbucksKorea)

---

오늘은 스와이퍼와 gsap을 사용해보았다.<br/>

gsap을 통해서 메인 배너에 애니메이션을 불어넣었고, 스와이퍼를 통해서 토글과 공지사항을 만들어보았다.<br/>

### 겪어본 오류<br/>

이번에는 오류가 좀 많이 발생했었다. 간단한 오류도 있었고, 알고보면 쉬운 오류도 있었지만 해결하는데에 역시 오래걸렸다. 구글링 끝에 해결한 것도 있고, 혼자서 해결한 것도 있지만, 공통적인 것은 **해결하고 나면 역시 뿌듯하다는 것**이다!
<br/>
<br/>

---
### 첫 번째 오류

![오류1](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_1.jpg?raw=true)

메인 배너에서 사진이 잘렸던 오류가 났었다.<br/>
하지만 그건 오류가 아니었다...


---


![오류원인](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_1_cause_2.jpg?raw=true)

스크롤바 위치가 이상하다 싶었는데..<br/>


![오류수정](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_1_fix.jpg?raw=true)

사실 오류따위는 발생하지 않았다.. 그냥 내가 스크롤바 밑에다 두고 이미지 잘렸다고 생각한 것이었다...<br/>
<br/>

---

### 두 번째 오류

![오류2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_2.jpg?raw=true)

의도대로라면 세로로 정렬되어야하지만 자꾸 파란 글씨들이 가로로 정렬되고있었다.<br/>

![오류2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_2_cause_1.jpg?raw=true)<br/>

이것이 그 당시의 html 코드였다.<br/>
원인을 분석해보니 div의 위치가 문제였다.<br/>

![오류수정2_2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_2_fix_1.jpg?raw=true)<br/>

스와이퍼 슬라이드 요소들이 스와이퍼 컨테이너 밖에 나와있었다. 이들을 안으로 넣어주었다.<br/>

![오류수정2_2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_2_fix_2.jpg?raw=true)
<br/>
오케이 잘 해결되었다. 모두 스와이퍼를 쓸 수 있도록 세로로 정렬되었다.
<br/>
<br/>

---

### 세 번째 오류<br/>

아마 오늘 발생한 오류 중에서 가장 해결하기가 힘들었던 오류 같다..<br/>

![오류3](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_3.jpg?raw=true)
<br/>

스와이퍼를 썼더니 저렇게 목록이 계속 여러개 출력되는 것이 아닌가.. 검은 영역 안에서 한 줄씩만 출력되도록 설정했는데..<br/>
<br/>

![오류3 코드](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_3_cause.jpg?raw=true)
<br/>

html도 이상이 없었고, 자바스크립트도, CSS도 이상이 없었다. 인터넷 강의에서 하라는 대로 코딩을 그대로 쳤는데도 계속 오류가 발생하니 미칠 것 같았다.<br/>
그렇게 1~2시간을 보냈던 것 같다.
<br/>

그러다가 구글링을 하면서 힌트를 얻었다.<br/>

생각해보니 저거 영역은 잘 설정되었지만 지금 문장들이 영역 밖으로 출력이 되고 있는 것이잖아?<br/>
즉, 검은 영역 밖에 있는 문장들은 _**Overflow 상태**_ 라는 것이다.<br/>

![오류3 수정](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_3_fix.jpg?raw=true)<br/>

혹시나 싶어서 CSS에서 해당 클래스에서 Overflow된 부분은 가려지도록 문장을 추가했다.<br/>

![오류3 결과](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_3_fix_2.jpg?raw=true)<br/>

<span style='font-size:3em; font-weight:700'> (내적 환호 중) </span>

<br/>
<br/>

---

### 네 번째 오류

스타벅스 프로모션 옆에 있는 토글 숨기기 버튼이 작동하지 않았다.<br/>

![오류4 코드](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_4_1.jpg?raw=true)

<br/>

![오류4 코드](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_4_2.jpg?raw=true)

<br/>

![오류4 코드](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_4_3.jpg?raw=true)

html, css, JS 모두 정상이었다.<br/>

아.. 그렇다고 생각했다..

![오류4 원인](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_4_cause.jpg?raw=true)

저기 위치에다가 표시해놓은 부분에서 커서 포인터가 작동하는 것이 아닌가...<br/>

결국 HTML 문제였다.

![오류4 수정](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_4_fix_1.jpg?raw=true)<br/>

두 번째 오류랑 똑같이 div 배치가 문제였다. 이렇게 수정하니까 정상적으로 위치가 바뀌었다.<br/>

![오류4 수정2](https://github.com/WookeyKim95/clone_StarbucksKorea/blob/main/error_capture/2022_02_25_4_fix_2.jpg?raw=true)<br/>

아참, 위에 수정한 html코드에 onclick이 있었는데 함수문제인가 싶어서 자바스크립트를 위와 같이 바꾸었다. 이렇게 자바스크립트를 해도 정상작동한다.<br/>

---

2일차는 1일차보다 오류가 많이 발생했다.<br/>

특히 두 번째 오류, 네 번째 오류는 같은 원인으로 오류가 발생했다. 이 부분을 조심하면서 코딩해야겠다.<br/>