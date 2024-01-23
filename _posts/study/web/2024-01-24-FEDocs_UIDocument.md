---
layout: post
title:  "[웹 개발 실무] 화면 설계서 작성하기"
subtitle:   
date: 2024-01-24 06:56:06 +0900
tags: web
categories: study
use_math: true
comments: true
related_posts:

---

# [웹 개발 실무] 화면 설계서 작성하기<br/>
<br/>

개발을 함에 있어서 JIRA와 같은 프로젝트 관리 및 협업툴을 사용할 수도 있지만,<br/>

프로젝트 진행과정에서 산출되는 표준화에 근간을 둔 기획서의 작성 및 관리도 필요하다.<br/>

그래서 우리는 요구사항 정의서, 화면 설계서, 기능 정의서, API 명세서 등 기획서를 작성하며,<br/>

이 중에서 프론트엔드 기획 시에는 화면 설계서를 작성하는 것이 필수이다.<br/>

자 그러면 화면 설계서 상에는 어떤 것이 들어가는지 한번 살펴보자.<br/>
<Br/>

[글 작성 내용 참고 링크](https://www.datachef.co.kr/post_webplanning/?q=YToyOntzOjEyOiJrZXl3b3JkX3R5cGUiO3M6MzoiYWxsIjtzOjQ6InBhZ2UiO2k6Mzt9&bmode=view&idx=5643844&t=board)
<br/>

## 표지<br/>
<Br/>

표지를 통해 기획서의 명칭을 정의하고 기획서의 버전(Version), 작성일자, 작성자 정보 등을 기록한다.<br/>

아울러 문서의 보안성 정도에 따라 보안의 단계나 열람권한 등을 표기하기도 한다.<br/>
<Br/>

## History<br/>
<Br/>

우리가 작성하는 화면설계서 또는 스토리보드는 필연적으로 고객, 사수, 팀장, 오너 또는 기획자 본인의 필요에 의해 수정이 이루어지게 된다.<br/>

이때 문서의 업데이트 내역이 관리되지 않는다면, 어떤 내용이 수정되었는지 기획자 본인은 물론 디자인/퍼블리싱/개발을 위해 문서를 보는 작업자들도 알 수가 없다.<br/>

이를 위해 업데이트 기록을 관리하는 History 양식이 존재하며 버전, 업데이트 날짜, 변경 또는 추가된 내용, 해당 변경이 적용된 페이지, 작성자 정보를 기록한다.<br/>
<br/>

## 서비스 개요<br/>
<br/>

기획자가 만드는 모든 문서에는 주제적 관점에 기인한 목적성이 있어야 한다.<br/>

기획서를 왜 작성했고 무엇을 지향하고 있는지를 정의해줘야 기획서 본연의 존재감이 생기게 됩니다.<br/>

이를 위해서는 아래 그림에서 보는 것처럼 기획배경과 목적, 기획을 통해 얻고자 하는 기대효과, 기획에서 고려된 주요 기능요약, 기타 전달해야 할 이슈를 정의해줘야 한다.<br/>

![서비스 개요](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile27.uf.tistory.com%2Fimage%2F9905AF445E01979B2E3BBC)

<br/>

## User Flow<br/>
<br/>

아래 그림을 보면 각 단계로 구분된 표와 표 안에 논리 프로세스가 구성되어 있는 것을 확인할 수 있다.<br/>
 
이 문서를 통해 단위 서비스 별로 고객의 이용 흐름을 도식화함으로써 한 눈에 서비스의 시작과 종료까지의 전 과정을 파악할 수 있다.<br/>
 
서비스의 규모에 따라 통상 1개 이상의 User flow가 존재하는데, 만일 서비스를 도식화하는 과정이 생략된다면 세부 UI를 설계하는 과정에서 중요한 흐름이 누락될 수도 있다.<Br>
 
또한 개발 시 전제 구조를 파악하는데 어려움을 겪을 수도 있다.<br/>

![유저 플로우](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile10.uf.tistory.com%2Fimage%2F99A017445E01979B2BBF43)

<br/>

## Logic process<br/>
<Br/>

앞선 User flow에서 단위 서비스의 단계 별 흐름을 개략적으로 정의했다면 Logic process는 단계 별로 상세한 흐름을 정의하는 문서이다.<br/>
 
이를 통해 정보의 수집이나 사용, 상황 별 논리적 대처방안을 정리하게 된다.<br/>
 
해당 문서는 개발 시 참고하는 내용이며, 개발 완료 후 서비스 검증을 위한 QA(Quality Assurance)과정에서도 사용된다.<br/>

경험이 부족한 개발자는 잘못 설계된 Logic process 그대로 개발하는 경우도 있으므로 보다 꼼꼼한 작성을 요하는 문서다.<br/>

![로직 프로세스](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F99B4393E5E0704FE225F5E)

<br/>

## UI 설계 양식<Br/>
<Br/>

어찌보면 가장 하이라이트.<br/>

UI를 어떻게 나타낼지 설계하는 문서이다.<br/>

피그마로도 작성할 수 있으며, 이를 통해서 대략적인 유저 플로우도 작성할 수 있게 된다.<br/>

![UI설계](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F99423A4A5E07191D2F6776)

<br/>